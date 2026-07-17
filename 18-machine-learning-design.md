# Machine Learning System Design
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Machine Learning pada sistem LSAR berfungsi sebagai lapisan analitik prediktif dan preskriptif di atas data akademik longitudinal yang telah dikumpulkan. Berbeda dengan LLM yang bersifat generatif (menghasilkan narasi), ML bersifat analitis: mengidentifikasi pola, tren, dan anomali dari data numerik siswa.

ML tidak menggantikan penilaian guru. Semua output ML berupa rekomendasi yang membutuhkan interpretasi dan keputusan guru.

---

# Tujuan Machine Learning

Machine Learning dikembangkan untuk membantu guru dalam:

- **Prediksi** — memperkirakan tren nilai siswa di semester mendatang
- **Deteksi Dini** — mengidentifikasi siswa yang berpotensi mengalami kesulitan akademik
- **Klasifikasi** — mengelompokkan siswa berdasarkan pola belajar untuk pengajaran yang lebih terarah
- **Rekomendasi** — memberikan saran intervensi yang sesuai dengan profil akademik siswa
- **Personalisasi** — membantu guru memahami kebutuhan individual siswa secara lebih mendalam

---

# Arsitektur ML

```
+------------------------------------------------------------------+
|                        DATA LAYER                                |
|  MySQL/MariaDB → Feature Store (Query Terstruktur)               |
+------------------------------------------------------------------+
                            │
                            ▼
+------------------------------------------------------------------+
|                     FEATURE ENGINEERING                           |
|  • Aggregation: rata-rata, tren, volatilitas nilai               |
|  • Temporal: delta antar semester, kecepatan perubahan           |
|  • Behavioral: rasio kehadiran, frekuensi prestasi               |
|  • Demografi: tidak digunakan untuk prediksi (etis)              |
+------------------------------------------------------------------+
                            │
                            ▼
+------------------------------------------------------------------+
|                      MODEL LAYER                                 |
|  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐     |
|  │ Regresi        │  │ Klasifikasi    │  │ Clustering     │     |
|  │ • Tren Nilai   │  │ • At-risk      │  │ • Pola Belajar │     |
|  │ • Proyeksi     │  │ • Status       │  │ • Kelompok     │     |
|  │   Akademik     │  │   Kelulusan    │  │   Siswa        │     |
|  └────────────────┘  └────────────────┘  └────────────────┘     |
+------------------------------------------------------------------+
                            │
                            ▼
+------------------------------------------------------------------+
|                   OUTPUT & INTERPRETATION                        |
|  • Dashboard visual (grafik tren, heatmap risiko)                |
|  • Label dan score untuk setiap siswa                            |
|  • Rekomendasi intervensi berbasis rule + ML                     |
|  • Selalu disertai penjelasan (explainable AI)                   |
+------------------------------------------------------------------+
                            │
                            ▼
+------------------------------------------------------------------+
|                    HUMAN IN THE LOOP                             |
|  Guru menerima/menolak/menindaklanjuti rekomendasi               |
+------------------------------------------------------------------+
```

---

# ML Use Cases

## UC-ML-01: Prediksi Tren Nilai (Regresi)

**Tujuan:** Memprediksi nilai rata-rata siswa pada semester mendatang berdasarkan historical scores.

**Input:**
- Rata-rata nilai pengetahuan per semester (knowledgeScore)
- Rata-rata nilai keterampilan per semester (skillsScore)
- Jumlah semester yang telah dilalui
- Mata pelajaran spesifik (opsional)

**Output:**
- Prediksi nilai rata-rata semester depan (dengan confidence interval)
- Visualisasi tren: grafik garis historical + prediksi

**Model:** Linear Regression / Polynomial Regression / LightGBM

**Metrik:** MAE (Mean Absolute Error), RMSE

**Target MVP:** Threshold-based (sederhana) → ditingkatkan ke model ML setelah data terkumpul

---

## UC-ML-02: Early Warning System (Klasifikasi)

**Tujuan:** Mengidentifikasi siswa yang berisiko mengalami penurunan akademik signifikan atau tidak naik kelas.

**Input:**
- Tren nilai (3 semester terakhir)
- Rasio kehadiran (sakit + izin + alpha)
- Jumlah prestasi per semester
- Catatan guru (diproses via NLP sederhana — panjang catatan, sentimen)
- Perubahan kelas/wali kelas

**Output:**
- Label risiko: **Aman**, **Waspada**, **Kritis**
- Faktor kontributor utama (misal: absensi tinggi, penurunan Matematika)
- Rekomendasi tindakan awal

**Model:** Random Forest / XGBoost / Logistic Regression

**Metrik:** Precision, Recall, F1-Score (fokus recall — jangan sampai ada siswa at-risk terlewat)

**Threshold Label:**
- **Aman**: tren nilai naik/stabil ≥ 75, kehadiran ≥ 90%
- **Waspada**: tren nilai turun > 10% atau kehadiran < 85%
- **Kritis**: tren nilai turun > 20% dan kehadiran < 80%

---

## UC-ML-03: Clustering Pola Belajar (Unsupervised)

**Tujuan:** Mengelompokkan siswa berdasarkan pola akademik untuk membantu guru merancang pengajaran yang lebih terarah.

**Input:**
- Rata-rata nilai per jenis mata pelajaran (eksakta, bahasa, sosial)
- Konsistensi nilai antar semester (standar deviasi)
- Rasio pengetahuan vs keterampilan
- Kehadiran

**Output:**
- Kelompok siswa (3-5 cluster)
- Profil setiap cluster (misal: "Unggul Eksakta", "Konsisten Tinggi", "Perlu Bimbingan")
- Anggota setiap kelompok

**Model:** K-Means / DBSCAN / Gaussian Mixture

**Metrik:** Silhouette Score, Inertia

**Target MVP:** Rule-based clustering → K-Means setelah data ≥ 200 siswa

---

## UC-ML-04: Rekomendasi Intervensi (Hybrid Rule + ML)

**Tujuan:** Memberikan saran tindakan yang spesifik untuk setiap siswa berdasarkan profil akademiknya.

**Pendekatan:**
- **Rule-based**: Jika nilai turun di Matematika → rekomendasi latihan soal tambahan
- **ML-enhanced**: Cari pola siswa serupa yang berhasil naik nilainya → rekomendasikan strategi yang sama

**Output:** 1-2 rekomendasi tindakan per siswa, dengan prioritas

---

# Feature Engineering

## Feature Categories

| Kategori      | Fitur                                          | Sumber Data           |
|---------------|------------------------------------------------|-----------------------|
| Akademik      | Rata-rata knowledgeScore per semester          | SubjectScore          |
| Akademik      | Rata-rata skillsScore per semester             | SubjectScore          |
| Akademik      | Standar deviasi nilai antar semester           | SubjectScore          |
| Akademik      | Delta nilai (semester[n] - semester[n-1])      | SubjectScore          |
| Akademik      | Jumlah mata pelajaran di bawah KKM             | SubjectScore          |
| Kehadiran     | Rasio kehadiran per semester                   | Attendance            |
| Kehadiran     | Tren alpha (tanpa keterangan)                  | Attendance            |
| Prestasi      | Jumlah prestasi per semester                   | Achievement           |
| Prestasi      | Tren prestasi (akademik vs non-akademik)       | Achievement           |
| Kesehatan     | Jumlah catatan kesehatan                       | HealthRecord          |
| Longitudinal  | Jumlah semester telah ditempuh                 | SemesterRecord        |
| Longitudinal  | Jumlah pergantian wali kelas                   | ClassAuditLog         |

## Feature Pipeline

```sql
-- Contoh query feature untuk satu siswa
SELECT
  AVG(sk.knowledge_score) as avg_knowledge,
  AVG(sk.skills_score) as avg_skills,
  STDDEV(sk.knowledge_score) as volatility,
  AVG(sk.knowledge_score) - LAG(AVG(sk.knowledge_score))
    OVER (ORDER BY sr.semester) as score_delta,
  SUM(a.sick + a.permission + a.absent) as total_absence,
  COUNT(DISTINCT ach.id) as achievement_count
FROM semester_record sr
JOIN subject_score sk ON sk.semester_record_id = sr.id
LEFT JOIN attendance a ON a.semester_record_id = sr.id
LEFT JOIN achievement ach ON ach.semester_record_id = sr.id
WHERE sr.student_id = :studentId
GROUP BY sr.id, sr.semester
ORDER BY sr.semester
```

## Feature Store (MVP)

Untuk MVP, feature store cukup berupa query SQL terstruktur di backend (tidak perlu Redis/feature store terpisah). Query dikumpulkan di `src/modules/ml/features.ts`.

---

# Model Pipeline

## Alur Training & Inference

```
DATA COLLECTION
      │
      ▼
FEATURE ENGINEERING (SQL → JSON)
      │
      ├── TRAINING MODE (menjelang akhir semester)
      │     │
      │     ▼
      │   Split Data (Train/Test)
      │     │
      │     ▼
      │   Train Model → Evaluasi → Save Model
      │
      └── INFERENCE MODE (real-time / batch)
            │
            ▼
          Load Model → Predict → Save to PredictedOutcome
            │
            ▼
          Tampilkan di Dashboard Guru
```

## Model Storage (MVP)

Untuk MVP, model ML disimpan sebagai file serialized:
- **Prototype**: Python pickle/joblib (dari notebook Jupyter/Colab)
- **Integrasi**: Export ke ONNX, load di backend via `onnxruntime-node`
- **Fallback**: Rule-based jika model belum trained

## Schedule Training

| Skenario           | Trigger                          | Mode              |
|--------------------|----------------------------------|-------------------|
| Initial training   | Manual (admin)                   | Notebook (Colab)  |
| Semester baru      | AcademicYear isActive = true     | Batch (backend)   |
| Real-time prediksi | Request dashboard                | Inference (online)|

---

# Database Schema untuk ML

Model ML membutuhkan tabel tambahan untuk menyimpan hasil prediksi:

## PredictedOutcome

Deskripsi: Menyimpan hasil prediksi ML untuk setiap siswa per semester.

| Field       | Type         | Constraint          |
|-------------|--------------|---------------------|
| id          | UUID         | PK                  |
| student_id  | UUID         | FK → Student        |
| semester_id | UUID         | FK → AcademicYear   |
| model_type  | ENUM         | NOT NULL            |
| score       | FLOAT        | Nilai prediksi      |
| label       | VARCHAR(20)  | Label klasifikasi   |
| confidence  | FLOAT        | Confidence score    |
| features    | JSON         | Feature snapshot    |
| is_active   | BOOLEAN      | DEFAULT true        |
| created_at  | TIMESTAMP    | DEFAULT now()       |

ENUM ModelType: `TREND_PREDICTION`, `RISK_CLASSIFICATION`, `BEHAVIOR_CLUSTER`

Constraint: `@@unique([student_id, semester_id, model_type, is_active])`

## MLModel

Deskripsi: Menyimpan metadata model ML yang telah di-training.

| Field       | Type         | Constraint          |
|-------------|--------------|---------------------|
| id          | UUID         | PK                  |
| name        | VARCHAR(100) | NOT NULL            |
| model_type  | ENUM         | NOT NULL            |
| version     | Int          | NOT NULL            |
| file_path   | VARCHAR(255) | Path ke model file  |
| metrics     | JSON         | MAE, RMSE, F1, etc. |
| feature_list| JSON         | Daftar fitur        |
| is_active   | BOOLEAN      | DEFAULT false       |
| trained_at  | TIMESTAMP    | DEFAULT now()       |

---

# Integrasi dengan Arsitektur Existing

```
Backend (Elysia)
    │
    ├── AI Module (LLM) ──── OpenAI / Gemini API
    │
    └── ML Module ──── onnxruntime ──── Model (.onnx)
         │
         ├── features.ts      # Feature engineering queries
         ├── predictor.ts     # Inference pipeline
         ├── trainer.ts       # Training pipeline (batch)
         └── models/          # Serialized model files
```

## ML Module Structure (Backend)

```
src/modules/ml/
├── ml.controller.ts        # Endpoint prediksi & training
├── ml.service.ts           # Orchestrator ML
├── ml.validation.ts        # Validasi input
├── features.ts             # Feature engineering queries
├── predictor.ts            # Load model → predict
├── trainer.ts              # Training pipeline
└── models/                 # .onnx model files (gitignored)
```

## API Endpoints ML

| Method | Endpoint                              | Role | Keterangan                    |
|--------|---------------------------------------|------|-------------------------------|
| POST   | `/ml/predict/student/:id/trend`       | GURU | Prediksi tren nilai           |
| GET    | `/ml/risk-assessment/class/:id`       | GURU | Early warning per kelas       |
| GET    | `/ml/clusters/class/:id`              | GURU | Clustering pola belajar       |
| POST   | `/ml/train/:modelType`                | ADMIN| Training model                |
| GET    | `/ml/models`                          | ADMIN| Daftar model & metrik         |

---

# Etika & Fairness ML

1. **Tidak ada diskriminasi**: Fitur demografi (gender, asal) tidak digunakan sebagai feature.
2. **Explainable**: Setiap prediksi harus disertai faktor kontributor utama.
3. **Akurasi bukan segalanya**: False negative (siswa at-risk tidak terdeteksi) lebih berbahaya dari false positive.
4. **Data minimal**: Model tidak boleh membutuhkan data di luar yang sudah dikumpulkan sistem.
5. **Guru sebagai otoritas**: Label risiko tidak boleh digunakan sebagai keputusan otomatis (misal: tidak naik kelas).
6. **Transparansi**: Guru bisa melihat fitur apa yang menyebabkan prediksi tertentu.

---

# Roadmap Pengembangan ML

| Fase  | Fitur ML                                           | Teknologi                    | Target                  |
|-------|----------------------------------------------------|------------------------------|-------------------------|
| MVP   | Rule-based alert (threshold score + absensi)       | Backend logic (no model)     | LIDM 2026               |
| Fase 2| Prediksi tren nilai (regresi)                      | Python (Colab) → ONNX        | Pasca LIDM (1 bulan)   |
| Fase 2| Early warning system (klasifikasi)                 | Scikit-learn → ONNX          | Pasca LIDM (2 bulan)   |
| Fase 3| Clustering pola belajar                            | Scikit-learn                 | Pasca LIDM (3 bulan)   |
| Fase 3| Rekomendasi intervensi hybrid                      | Rule + ML                    | Pasca LIDM (4 bulan)   |
| Fase 4| Pipeline training otomatis per semester            | Backend scheduler            | Pasca LIDM (6 bulan)   |
| Fase 4| Dashboard analitik ML real-time                    | Frontend chart library       | Pasca LIDM (6 bulan)   |

---

# MVP ML: Rule-based Alert System

Untuk MVP LIDM 2026, ML diimplementasikan dalam bentuk paling sederhana:

## Aturan Early Warning (Rule-based)

```typescript
function assessRisk(studentId: string): RiskAssessment {
  const data = getAcademicData(studentId);

  const scoreDecline = data.semesters.length >= 2
    ? data.semesters[last].avgKnowledge - data.semesters[last-1].avgKnowledge
    : 0;

  const attendanceRate = data.attendance.present /
    (data.attendance.present + data.attendance.sick +
     data.attendance.permission + data.attendance.absent);

  // Rule-based classification
  if (scoreDecline < -20 && attendanceRate < 0.8) return 'KRITIS';
  if (scoreDecline < -10 || attendanceRate < 0.85) return 'WASPADA';
  return 'AMAN';
}
```

## Keuntungan Rule-based MVP

1. Tidak memerlukan model ML — implementasi cepat
2. Mudah dijelaskan ke guru (transparan)
3. Tidak memerlukan data training historis
4. Bisa ditingkatkan ke ML seiring terkumpulnya data

---

# Catatan Implementasi

1. **Data Privacy**: Semua processing ML dilakukan di backend (on-premise), tidak ada data siswa yang dikirim ke pihak ketiga untuk ML (beda dengan LLM yang via API).
2. **Model Size**: Model ONNX harus < 50MB untuk kompatibilitas dengan runtime Bun.
3. **Fallback**: Selalu siapkan fallback rule-based jika model gagal load.
4. **Monitoring**: Log semua prediksi untuk audit dan evaluasi model.
5. **Iterative**: ML dimulai dari yang paling sederhana (regresi linear) sebelum naik ke model kompleks.
6. **Format JSON untuk Features**: Feature vector disimpan sebagai JSON di PredictedOutcome untuk reprodusibilitas.
