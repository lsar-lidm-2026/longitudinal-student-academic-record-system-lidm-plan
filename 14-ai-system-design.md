# AI System Design
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Artificial Intelligence pada sistem ini berfungsi sebagai asisten bagi guru dalam mengolah data akademik siswa menjadi informasi yang lebih mudah dipahami.

AI tidak menggantikan peran guru dalam melakukan penilaian maupun pengambilan keputusan.

Seluruh hasil yang dihasilkan AI tetap dapat diperiksa, diubah, maupun diabaikan oleh guru sebelum digunakan.

Dengan demikian, Artificial Intelligence berperan sebagai decision support system, bukan decision maker.

---

# Tujuan AI

Artificial Intelligence dikembangkan untuk membantu guru dalam:

- memahami perkembangan akademik siswa
- menyusun deskripsi rapor
- mempermudah proses pergantian wali kelas
- mempercepat penyusunan administrasi Buku Induk

---

# Arsitektur AI

```text
                 Guru / Kepala Sekolah
                   │
                   ▼
       Longitudinal Academic Record
                   │
         ┌─────────┴──────────────┐
         │                        │
         ▼                        ▼
  AI Processing              ML Processing
  (LLM - Generatif)          (Analitik - Prediktif)
         │                        │
         │                        ├── Prediksi Tren Nilai
         │                        ├── Early Warning System
         │                        ├── Clustering Pola Belajar
         │                        └── Rekomendasi Intervensi
         │                        │
         ▼                        ▼
  Student Summary           Dashboard Analitik
  Draft Deskripsi           Grafik Tren
  Transition Summary        Heatmap Risiko
         │                        │
         └──────────┬─────────────┘
                    ▼
            Review oleh Guru
                    │
                    ▼
          Digunakan dalam Sistem
```

---

# Komponen AI

## 1. Input Data

Artificial Intelligence menggunakan data yang telah tersedia pada database sistem.

Data tersebut meliputi:

- Biodata siswa
- Nilai setiap mata pelajaran
- Nilai rata-rata
- Kehadiran
- Prestasi
- Data kesehatan
- Catatan guru

AI tidak menerima input secara manual selain permintaan dari guru.

---

## 2. AI Processing

Setelah guru memilih siswa, sistem akan mengumpulkan seluruh data akademik yang berkaitan.

Data tersebut kemudian disusun menjadi prompt terstruktur sebelum dikirim ke Large Language Model (LLM).

LLM menghasilkan narasi berdasarkan data yang diberikan tanpa melakukan perubahan terhadap data asli.

---

## 3. Output AI

Artificial Intelligence menghasilkan tiga fitur utama.

### AI Student Summary

Merangkum perkembangan akademik siswa secara singkat.

Contoh informasi:

- mata pelajaran yang paling menonjol
- peningkatan prestasi
- kecenderungan kehadiran
- prestasi siswa
- catatan perkembangan

---

### AI Draft Deskripsi

Menghasilkan draft deskripsi rapor berdasarkan data akademik siswa.

Guru tetap dapat mengubah hasil yang diberikan AI sebelum digunakan.

---

### AI Student Transition Summary

Menghasilkan ringkasan perkembangan siswa untuk membantu wali kelas berikutnya memahami kondisi siswa secara lebih cepat.

Ringkasan ini memuat informasi penting seperti:

- perkembangan akademik
- kekuatan siswa
- area yang masih perlu ditingkatkan
- catatan penting guru sebelumnya

---

# AI Workflow

```text
Guru

↓

Memilih Siswa

↓

Sistem Mengambil Data Akademik

↓

Menyusun Prompt

↓

Large Language Model

↓

Generate Narasi

↓

Guru Review

↓

Guru Edit (Opsional)

↓

Simpan
```

---

# Human in the Loop

Setiap keluaran Artificial Intelligence harus melalui proses validasi guru.

Guru memiliki hak penuh untuk:

- menerima hasil AI
- mengubah hasil AI
- menghapus hasil AI
- membuat ulang hasil AI

Dengan demikian seluruh keputusan akademik tetap berada pada guru.

---

# Kelebihan Pendekatan

Pendekatan ini memiliki beberapa keunggulan.

- AI tidak mengubah data akademik.
- AI hanya menghasilkan narasi.
- Guru tetap menjadi pengambil keputusan.
- Hasil AI mudah diperiksa kembali.
- Risiko kesalahan dapat diminimalkan.

---

# Batasan Sistem

Pada MVP ini Artificial Intelligence belum melakukan:

- prediksi prestasi siswa
- deteksi dini siswa berisiko
- chatbot
- pencarian dokumen menggunakan RAG
- analisis dokumen PDF
- OCR Buku Induk
- analisis gambar

Seluruh fitur tersebut merupakan rencana pengembangan pada tahap berikutnya.

---

---

# Prompt Engineering

Setiap fitur AI menggunakan prompt terstruktur yang disusun oleh backend. Prompt terdiri dari:

1. **System Prompt** — instruksi umum untuk LLM
2. **Context** — data akademik siswa yang relevan
3. **Format Output** — format yang diinginkan

## Prompt AI Student Summary

```
System Prompt:
Anda adalah asisten administrasi pendidikan yang membantu guru SD.
Buat ringkasan perkembangan siswa berdasarkan data akademik berikut.
Ringkasan harus:
- Bahasa Indonesia yang baik dan benar
- Fokus pada kekuatan dan area pengembangan
- Objektif berdasarkan data
- Maksimal 3 paragraf
- Tidak mengandung informasi yang tidak ada dalam data

Context:
Nama Siswa: {student.name}
Kelas: {class.name}
Semester: {semester} (Ganjil/Genap)
Tahun Ajaran: {academicYear}

Nilai:
{subjectScores.map(s => `- ${s.subjectName}: Pengetahuan ${s.knowledgeScore}, Keterampilan ${s.skillsScore}`)}

Kehadiran:
- Sakit: {attendance.sick} hari
- Izin: {attendance.permission} hari
- Alpha: {attendance.absent} hari

Prestasi:
{achievements.map(a => `- ${a.title} (${a.type})`)}

Catatan Guru:
{teacherNote}
```

## Prompt AI Draft Deskripsi Rapor

```
System Prompt:
Anda adalah asisten guru SD yang membantu menyusun deskripsi rapor.
Buat draft deskripsi rapor untuk setiap mata pelajaran.
Deskripsi harus:
- Bahasa Indonesia yang baik
- Mencakup aspek pengetahuan dan keterampilan
- Memberikan gambaran objektif
- Disertai saran pengembangan yang konstruktif
- Tidak menggunakan kata-kata negatif
- Per semester yang sama dengan data

Context:
Nama Siswa: {student.name}
Kelas: {class.name}
Semester: {semester}

Nilai Mata Pelajaran:
{subjectScores.map(s => `- ${s.subjectName}: Pengetahuan ${s.knowledgeScore}, Keterampilan ${s.skillsScore}. Catatan: ${s.notes || '-'}`)}

Total Kehadiran: Sakit {s}, Izin {p}, Alpha {a}

Format Output:
**{subjectName}**: [deskripsi narasi 2-3 kalimat]
(ulangi untuk setiap mata pelajaran)
```

## Prompt AI Student Transition Summary

```
System Prompt:
Anda adalah asisten yang membantu serah terima wali kelas di SD.
Buat ringkasan transisi untuk wali kelas baru tentang siswa ini.
Ringkasan harus:
- Bahasa Indonesia yang baik
- Fokus pada informasi yang berguna untuk guru baru
- Mencakup kekuatan akademik, area pengembangan, dan catatan penting
- Objektif berdasarkan data
- Maksimal 4 paragraf

Context:
Nama Siswa: {student.name}
Riwayat Kelas: {gradeHistory} (kelas 1 sampai kelas saat ini)

Riwayat Nilai per Semester:
{semesterRecords.map(r => `Semester ${r.semester} ${r.academicYear}: Rata-rata ${r.average}`)}

Prestasi Selama Belajar:
{allAchievements.map(a => `- ${a.title} (${a.type}, Semester ${a.semester})`)}

Kehadiran (Rata-rata):
- Sakit: {avgSick}/semester
- Izin: {avgPermission}/semester

Catatan Penting:
{allNotes}

Format Output:
1. Profil Singkat: [1 paragraf]
2. Kekuatan Akademik: [1-2 kalimat]
3. Area Pengembangan: [1-2 kalimat]
4. Catatan Penting untuk Wali Kelas Baru: [1-2 kalimat]
```

---

# Error Handling LLM

## Skenario Error

| Skenario                   | Penyebab                        | Handling                                 |
|----------------------------|---------------------------------|------------------------------------------|
| Timeout                    | LLM API lambat                  | Retry 1x, timeout 30s                    |
| Rate Limit                 | Terlalu banyak request          | Exponential backoff, informasikan user   |
| Invalid Response           | Output tidak sesuai format      | Validasi format, minta regenerate        |
| Empty Response             | LLM return kosong               | Retry dengan prompt lebih spesifik       |
| API Key Invalid            | Konfigurasi salah               | Log error, tampilkan pesan ke admin      |
| Model Unavailable          | Model sedang down               | Fallback ke model alternatif             |

## Retry Strategy

```
Request ke LLM
  → Success → return response
  → Timeout → tunggu 2 detik → retry (max 1x)
    → Success → return response
    → Timeout → return AI_ERROR (502)
```

## Content Validation

Setelah menerima response dari LLM, backend harus memvalidasi:

1. Response tidak kosong
2. Response dalam format yang diharapkan (teks narasi)
3. Tidak mengandung konten yang tidak pantas (optional)

Jika validasi gagal, backend mengembalikan error yang memungkinkan user melakukan regenerate.

---

# Rate Limiting & Cost Optimization

## Strategi

| Strategi             | Implementasi                                |
|----------------------|---------------------------------------------|
| Cache hasil          | Simpan di AiSummary, jika sudah ada dan     |
|                      | data tidak berubah, gunakan hasil lama      |
| Debounce             | Jangan izinkan regenerate dalam 5 detik     |
| Model hemat          | Gunakan model kecil (gpt-4o-mini / Gemini  |
|                      | Flash) untuk MVP                            |
| Batch (Transition)   | Proses seluruh siswa dalam satu kelas       |
|                      | dengan satu prompt (jika memungkinkan)      |

---

# AI Safety & Privacy

1. **Data Privacy**: Data siswa hanya dikirim ke LLM untuk satu kali generate, tidak disimpan oleh pihak ketiga.
2. **No Training**: LLM API yang digunakan tidak menggunakan data yang dikirim untuk training model (OpenAI API, Gemini API).
3. **Human Oversight**: Setiap hasil AI harus direview guru sebelum digunakan (isFinal = true).
4. **No Hallucination**: Prompt dirancang untuk meminimalkan halusinasi dengan membatasi output hanya berdasarkan data yang diberikan.
5. **Logging**: Semua request dan response AI dicatat (tanpa data sensitif) untuk audit.

---

---

# Integrasi dengan Machine Learning

Sistem LSAR mengintegrasikan dua jenis kecerdasan buatan:

| Aspek              | AI (LLM)                                    | ML (Machine Learning)                       |
|--------------------|----------------------------------------------|---------------------------------------------|
| **Sifat**          | Generatif — menghasilkan teks baru           | Analitik — menemukan pola dari data         |
| **Input**          | Data akademik + prompt                       | Feature vector numerik                      |
| **Output**         | Narasi (ringkasan, draft deskripsi)          | Prediksi, label risiko, cluster             |
| **Model**          | LLM eksternal (OpenAI/Gemini)                | Model statistik/ML (ONNX lokal)             |
| **Data Privacy**   | Data dikirim ke API eksternal                | Processing lokal, data tidak keluar         |
| **Frekuensi**      | On-demand (guru klik generate)               | Batch per semester + real-time              |
| **Human Review**   | Wajib (isFinal)                              | Wajib (guru konfirmasi prediksi)            |
| **MVP**            | 3 fitur AI                                   | Rule-based alert system                     |

Hubungan antara AI dan ML:

```
Data Akademik → Feature Engineering → ML Models
                                       │
                          ┌────────────┴────────────┐
                          │                         │
                          ▼                         ▼
                    ML Output                  AI Prompt Context
                    (prediksi,                  (data + insight ML
                     label risiko)               sebagai konteks)
                          │                         │
                          └────────┬────────────────┘
                                   ▼
                            Dashboard Guru
```

Dengan integrasi ini, LLM dapat menggunakan insight dari ML sebagai konteks tambahan. Contoh: saat membuat Student Summary, LLM tidak hanya menerima data mentah tetapi juga insight "siswa ini mengalami penurunan 15% di Matematika" (dari ML).

---

# Roadmap Pengembangan AI & ML

| Fase  | AI (LLM)                                        | ML (Machine Learning)                   | Target            |
|-------|--------------------------------------------------|------------------------------------------|-------------------|
| MVP   | Student Summary, Draft Deskripsi, Transition Summary | Rule-based alert (threshold)        | LIDM 2026         |
| Fase 2| Chatbot AI, RAG, Vector Database                 | Prediksi Tren Nilai (regresi)            | Pasca LIDM (1-2 bln) |
| Fase 3| —                                                | Early Warning System (klasifikasi)       | Pasca LIDM (3 bln)  |
| Fase 3| —                                                | Clustering Pola Belajar                  | Pasca LIDM (4 bln)  |
| Fase 4| AI + ML terintegrasi penuh di dashboard          | Pipeline training otomatis               | Pasca LIDM (6 bln)  |

Dokumen terpisah: [18-machine-learning-design.md](./18-machine-learning-design.md) untuk detail arsitektur, pipeline, dan implementasi ML.

---

# Kesimpulan

Artificial Intelligence pada sistem ini dirancang sebagai pendamping guru dalam mengolah riwayat akademik siswa menjadi informasi yang lebih ringkas, mudah dipahami, dan siap digunakan dalam proses administrasi.

Pendekatan ini dipilih agar sistem tetap sederhana, mudah dikembangkan, dan sesuai dengan ruang lingkup Minimum Viable Product (MVP) pada Lomba Inovasi Digital Mahasiswa (LIDM), tanpa mengubah mekanisme administrasi sekolah yang tetap mengikuti regulasi yang berlaku.