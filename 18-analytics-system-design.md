# Analytics System Design
## Longitudinal Student Academic Record

> **Catatan:** Dokumen ini awalnya bernama "Machine Learning Design." Setelah evaluasi kode,
> istilah "Machine Learning" diganti dengan "Analytics" karena apa yang diimplementasikan
> adalah **rule-based scoring dan statistik deskriptif**, bukan supervised machine learning.
>
> Satu-satunya algoritma unsupervised learning yang legitimate adalah K-Means clustering.
> Lihat bagian [#Kejujuran Intelektual](#kejujuran-intelektual) untuk penjelasan lengkap.

# Pendahuluan

Analytics Engine pada sistem LSAR berfungsi sebagai lapisan analitik prediktif dan
deskriptif di atas data akademik longitudinal yang telah dikumpulkan. Berbeda dengan
LLM yang bersifat generatif (menghasilkan narasi), analytics bersifat analitis:
mengidentifikasi pola, tren, dan anomali dari data numerik siswa.

**Tidak ada supervised machine learning** dalam sistem ini karena:
1. Tidak ada ground truth labels (data historis "siswa ini beneran bermasalah")
2. Decision tree yang dilatih pada synthetic labels adalah **teater ML** — lihat
   [dokumentasi teknis](../apps/backend/src/modules/ml/scoring-engine.ts)
3. Yang ada adalah **feature computation + weighted rules + statistik OLS**

---

# Komponen Analytics

## 1. Risk Assessment — Rule-based Scoring

**File:** `scoring-engine.ts`

Early Warning System menggunakan **weighted scoring engine** dengan bobot yang
terdokumentasi dan transparan. BUKAN machine learning.

### Bobot Risiko

| Faktor | Bobot Maks | Threshold | Rasional Pedagogik |
|--------|------------|-----------|-------------------|
| Nilai di bawah KKM | 35 poin | avgKnowledge < 70 | Nilai akademik indikator paling kuat |
| Ketidakhadiran tinggi | 25 poin | rata-rata alpha > 5/semester | Kehadiran langsung mempengaruhi pembelajaran |
| Tren menurun | 20 poin | scoreDelta < -10 | Trend negatif menunjukkan masalah berkelanjutan |
| Volatilitas tinggi | 10 poin | scoreVolatility > 15 | Performa tidak stabil |
| Kurang prestasi | 10 poin | 0 prestasi dalam ≥2 semester | Engagement rendah |

### Level Risiko

| Skor | Level | Tindakan |
|------|-------|----------|
| 0-24 | Aman | Pemantauan rutin |
| 25-49 | Waspada | Intervensi ringan, koordinasi orang tua |
| 50-100 | Kritis | Intervensi intensif, konseling |

---

## 2. Trend Prediction — Linear Regression (OLS)

**File:** `models/linear-regression.ts`

Menggunakan Ordinary Least Squares regression untuk memprediksi nilai semester depan.
Rumus OLS standar:

```
m = (n·Σxy - Σx·Σy) / (n·Σx² - (Σx)²)
b = ȳ - m·x̄
y = mx + b
```

### Output
- **Slope**: Kecepatan perubahan nilai per semester
- **R² Real**: Koefisien determinasi — proporsi varians yang bisa dijelaskan model.
  Tidak di-hardcode (tidak seperti implementasi sebelumnya yang memfabrikasi R² = 0.6).
- **Prediksi**: Nilai semester depan, di-clamp ke [0, 100]

### Caveat
- Minimal 2 data points untuk menghasilkan regresi
- R² tidak reliable untuk n < 5

---

## 3. Behavior Clustering — K-Means (Unsupervised Learning)

**File:** `models/k-means.ts`

Satu-satunya algoritma yang benar-benar ML. Menggunakan Lloyd's algorithm dengan
k-means++ initialization.

### Detail
- **Jumlah cluster**: 3 (dapat dikonfigurasi)
- **Fitur**: avgKnowledge, avgSkills, totalAbsence, achievementCount
- **Normalisasi**: Setiap fitur dinormalisasi ke [0, 1]
- **Initialisasi**: K-Means++ (menjamin centroid awal terdiversifikasi)

### Evaluasi
- **Inertia**: Within-cluster sum of squared distances
- **Silhouette Score**: Approksimasi (berbasis centroid, bukan pairwise)
- **Cluster sizes**: Distribusi anggota per cluster

---

# Feature Engineering

## Feature Categories

| Kategori | Fitur | Sumber Data | Metode |
|----------|-------|-------------|--------|
| Akademik | Rata-rata knowledgeScore per semester | SubjectScore | Mean |
| Akademik | Rata-rata skillsScore per semester | SubjectScore | Mean |
| Akademik | Volatilitas nilai antar semester | SubjectScore | Std Dev |
| Akademik | Delta nilai terakhir | SubjectScore | Diff |
| Kehadiran | Total ketidakhadiran | Attendance | Sum |
| Kehadiran | Tren alpha | Attendance | Diff 2 points |
| Prestasi | Jumlah prestasi | Achievement | Count |
| Longitudinal | Jumlah semester | SemesterRecord | Count |

## Feature Pipeline

Feature computation dilakukan di `features.ts` dengan Prisma query. Detail:

1. Query semua semester records untuk satu siswa (ordered by academic year + semester)
2. Aggregate: rata-rata nilai, standar deviasi per semester
3. Temporal: delta antara semester terakhir dan sebelumnya
4. Behavioral: total absensi, tren alpha
5. Output: `StudentFeatures` interface

---

# Arsitektur

```
Backend (Elysia/Bun)
    │
    ├── AI Module (LLM) ──── OpenAI / Gemini API (generatif)
    │
    └── Analytics Module ──── onnxruntime ──── Model (.onnx)
         │
         ├── features.ts           # Feature computation
         ├── scoring-engine.ts     # Rule-based risk scoring
         ├── model-evaluator.ts    # Statistical metrics
         ├── trainer.ts            # K-Means training only
         ├── agent.ts              # LLM explanation (optional)
         └── models/               # K-Means centroids (.onnx + .json)
```

---

# Model Pipeline

## Alur Training & Inference

```
DATA (PostgreSQL via Prisma)
      │
      ▼
FEATURE COMPUTATION (features.ts)
      │
      ├── TRAINING (startup + periodic)
      │     │
      │     ▼
      │   K-Means Clustering → Centroids → ONNX export
      │     │
      │     ▼
      │   Persist to DB (MlModel table)
      │
      └── INFERENCE (request-time)
            │
            ▼
          Risk Scoring (rule-based) → PredictedOutcome
          Trend Prediction (OLS)    → PredictedOutcome
          Cluster Assignment (KNN)  → PredictedOutcome
```

## Training Schedule

| Model | Trigger | Frekuensi |
|-------|---------|-----------|
| K-Means | Startup + retrain interval | 6 jam (configurable) |
| Risk scoring | Request time | Real-time (deterministic) |
| Trend regression | Request time | Real-time (per-student) |

---

# API Endpoints

| Method | Endpoint | Role | Deskripsi |
|--------|----------|------|-----------|
| GET | `/ml/models` | ADMIN | Status model & metrik |
| GET | `/ml/risk/student/:id` | GURU | Risk assessment per siswa |
| GET | `/ml/risk/class/:id` | GURU | Risk assessment per kelas |
| GET | `/ml/trend/student/:id` | GURU | Trend prediksi per siswa |
| GET | `/ml/cluster/class/:id` | GURU | Clustering per kelas |
| GET | `/ml/outcomes` | ADMIN | Riwayat prediksi |
| POST | `/ml/train` | ADMIN | Retrain K-Means |
| GET | `/ml/eval` | ADMIN | Full evaluation report |

---

# Perbandingan: Before vs After

| Aspek | Sebelum (Before Roast) | Sesudah (After Roast) |
|-------|----------------------|-----------------------|
| Risk classification | Decision tree trained on synthetic labels | Rule-based scoring dengan bobot documented |
| Confidence score | 0.8 (hardcoded) | Tidak ada (deterministic) |
| R² trend | 0.6 (hardcoded) | Real R² dari OLS |
| Model evaluation | LLM disuruh nilaiin model sendiri | Statistical metrics (distribusi, IQR, silhouette) |
| ONNX decision tree | JSON blob disguised as .onnx | Dihapus |
| Eval agent | Suruh ChatGPT skor model | Dihapus |
| Training data | Synthetic labels dari heuristic yang sama | Tidak ada (rule-based) |

---

# Kejujuran Intelektual

## Kenapa Bukan Machine Learning?

**Supervised learning** membutuhkan **labeled data**. Dalam konteks ini, label adalah
"apakah siswa ini benar-benar bermasalah?" — yang kita tidak punya datanya.

Implementasi **sebelumnya** (yang di-roasting) membuat **synthetic labels** dari heuristic,
lalu melatih decision tree untuk memprediksi label tersebut. Hasilnya: decision tree
cuma belajar ngereplikasi heuristic — tidak ada learning yang terjadi.

## Apa Yang Kita Punya?

1. **Rule-based risk scoring** — transparan, bisa diaudit, documented weights
2. **K-Means clustering** — unsupervised learning yang legitimate
3. **Linear regression** — statistik OLS yang legitimate
4. **Feature computation** — aggregation queries yang solid

## Kapan Bisa Pake Real ML?

Ketika sistem sudah berjalan ≥1 tahun ajaran penuh DAN ada data:
- Nilai akhir semester
- Status naik kelas / tidak
- Catatan guru tentang intervensi yang berhasil

...baru kita bisa train supervised model yang legitimate.

---

# Catatan Implementasi

1. **Data Privacy**: Semua processing dilakukan di backend (on-premise), tidak ada
   data siswa yang dikirim ke pihak ketiga untuk analytics.
2. **ONNX Size**: Model ONNX K-Means sangat kecil (< 1MB).
3. **Fallback**: Risk scoring selalu tersedia (deterministic, tidak perlu model).
4. **Monitoring**: Semua hasil analytics disimpan di PredictedOutcome untuk audit.
5. **Reprodusibilitas**: Feature vector disimpan sebagai JSON di setiap prediksi.
6. **Graceful degradation**: Semua LLM explanation punya fallback template.
