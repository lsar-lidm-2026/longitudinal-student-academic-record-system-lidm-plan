# System Architecture
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

System Architecture menggambarkan bagaimana seluruh komponen sistem saling berinteraksi untuk mendukung proses administrasi akademik siswa.

Arsitektur ini dirancang mengikuti konsep Minimum Viable Product (MVP), sehingga hanya terdiri dari komponen yang benar-benar dibutuhkan untuk mendemonstrasikan solusi pada Lomba Inovasi Digital Mahasiswa (LIDM).

Sistem tidak dirancang untuk menggantikan Dapodik maupun sistem administrasi pemerintah, melainkan sebagai sistem pendamping (decision support system) yang membantu guru dalam mengelola riwayat akademik siswa dan menyusun administrasi Buku Induk Siswa.

---

# Tujuan Arsitektur

- Menyimpan riwayat akademik siswa secara longitudinal.
- Mempermudah guru mengelola administrasi akademik.
- Membantu AI menghasilkan ringkasan akademik.
- Membantu penyusunan Buku Induk secara manual.
- Menjaga sistem tetap sederhana dan mudah dikembangkan.

---

# Arsitektur MVP

```text
                           Guru / Operator / Admin / Kepsek
                                     │
                                     │
                            Web Browser (Next.js)
                                     │
                               REST API (HTTP/HTTPS)
                                     │
                                     ▼
                          Backend (Elysia - Bun)
          ┌──────────────────┬──────────────────┬─────────────────┐
          │                  │                  │                 │
          ▼                  ▼                  ▼                 ▼
   MySQL/MariaDB        AI Service         ML Engine        Authentication
   (Prisma ORM)        (LLM API)       (ONNX Runtime)      & Authorization
          │                  │                  │               (JWT)
          └──────────────────┴──────────────────┘
                     ▼
               Response ke Client
```

---

# Komponen MVP

## 1. User (Client)

Pengguna sistem terdiri dari:

- Administrator
- Operator Sekolah
- Guru / Wali Kelas
- Kepala Sekolah

Seluruh pengguna mengakses sistem melalui browser web.

---

## 2. Frontend (Next.js)

Frontend merupakan antarmuka yang digunakan oleh pengguna.

Teknologi

- Next.js 16 (React 19)
- TypeScript
- Tailwind CSS v4

Fungsi

- Login
- Dashboard
- Data Siswa
- Riwayat Akademik
- Input Semester
- AI Assistant
- Preview Buku Induk

Frontend hanya menampilkan data dan mengirim permintaan ke backend melalui REST API.

---

## 3. Backend (Elysia)

Backend bertanggung jawab menjalankan seluruh logika bisnis sistem.

Teknologi

- Elysia (Bun web framework)
- TypeScript
- Prisma ORM

Fungsi

- Autentikasi dan otorisasi pengguna (JWT)
- Validasi data
- CRUD data siswa, kelas, tahun ajaran
- CRUD nilai, kehadiran, prestasi, kesehatan
- Pengelolaan riwayat akademik longitudinal
- Komunikasi dengan AI Service (LLM API)
- Administrasi Buku Induk dan validasi data

---

## 4. Database (MySQL/MariaDB)

Database menyimpan seluruh data sistem.

Teknologi

- MySQL atau MariaDB
- Prisma ORM (type-safe database client)

Data yang disimpan

- Pengguna (User)
- Tahun Ajaran (AcademicYear)
- Kelas (Class)
- Siswa (Student)
- Riwayat Semester (SemesterRecord)
- Nilai Mata Pelajaran (SubjectScore)
- Kehadiran (Attendance)
- Prestasi (Achievement)
- Data Kesehatan (HealthRecord)
- Ringkasan AI (AiSummary)
- Log Audit Wali Kelas (ClassAuditLog)

Database menjadi single source of truth untuk seluruh riwayat akademik siswa.

---

## 5. AI Service (LLM - Generatif)

Artificial Intelligence digunakan sebagai asisten guru.

Teknologi

- Large Language Model (LLM API) — OpenAI API / Google Gemini API
- Bukan model yang di-hosting sendiri

Fungsi

- Generate AI Student Summary
- Generate AI Draft Deskripsi Rapor
- Generate AI Student Transition Summary

AI hanya membaca data dari backend dan menghasilkan narasi.
AI tidak mengubah data akademik siswa (read-only terhadap data sumber).

---

## 6. ML Engine (Analitik - Prediktif)

Machine Learning sebagai lapisan analitik prediktif di atas data longitudinal.

Teknologi

- Python (prototype/Colab) → ONNX Runtime (integrasi)
- Scikit-learn untuk training model
- onnxruntime-node untuk inference di backend Bun/Elysia

Fungsi

- Prediksi tren nilai siswa (regresi)
- Early Warning System — deteksi siswa at-risk (klasifikasi)
- Clustering pola belajar siswa (unsupervised)
- Rekomendasi intervensi berbasis rule + ML

Arsitektur ML:

```
Data Akademik → Feature Engineering → Model ONNX → Prediksi → Dashboard
                      ↓                                      ↓
              Feature Store (SQL)                     PredictedOutcome (DB)
```

Detail lengkap: [18-machine-learning-design.md](./18-machine-learning-design.md)

---

# Alur Kerja Sistem

## Login

User → Frontend → Backend → Database → Validasi → JWT → Dashboard

## Input Data Akademik

Guru → Frontend → Backend → Database → Data Tersimpan → Response

### Detail Alur

1. Guru memilih kelas dan siswa
2. Guru mengisi data nilai, kehadiran, prestasi, kesehatan
3. Frontend mengirim request POST/PUT ke backend
4. Backend memvalidasi hak akses (homeroom teacher check)
5. Backend menyimpan ke database via Prisma
6. Response dikembalikan ke frontend

## Generate AI

Guru → Frontend → Backend → Database (ambil riwayat) → AI Service → LLM API → Narasi → Backend → Frontend → Guru Review

### Detail Alur

1. Guru memilih siswa dan jenis AI (summary/draft/transition)
2. Backend mengumpulkan data akademik dari database
3. Backend menyusun prompt terstruktur
4. Backend mengirim prompt ke LLM API eksternal
5. LLM mengembalikan narasi
6. Backend menyimpan hasil sebagai draft (isFinal = false) di AiSummary
7. Frontend menampilkan hasil ke guru
8. Guru melakukan review, edit, atau regenerate
9. Guru menyimpan versi final (isFinal = true)

## Preview Buku Induk

Guru → Frontend → Backend → Database → Menyusun data → Preview → Guru menyalin ke Buku Induk Manual

---

# Hak Akses

## Administrator

- Kelola pengguna (CRUD User)
- Kelola kelas
- Kelola tahun ajaran

## Operator Sekolah

- Kelola data siswa
- Kelola perpindahan siswa antar kelas

## Guru / Wali Kelas

- Mengelola data akademik siswa di kelas yang diampu
- Melihat riwayat longitudinal siswa
- Menggunakan AI Assistant (summary, draft, transition)
- Melihat Preview Buku Induk
- Hak akses ditentukan oleh Teacher Assignment (homeroom_teacher_id)

## Kepala Sekolah

- Monitoring data akademik (read-only)
- Melihat riwayat siswa
- Melihat Preview Buku Induk

---

# Teknologi yang Digunakan (MVP)

| Komponen         | Teknologi                                   |
|------------------|---------------------------------------------|
| Frontend         | Next.js 16                                  |
| Bahasa           | TypeScript                                  |
| Styling          | Tailwind CSS v4                             |
| Backend          | Elysia (Bun web framework)                  |
| ORM              | Prisma                                      |
| Database         | MySQL / MariaDB                             |
| Autentikasi      | JWT (manual, tanpa library tambahan)        |
| AI (LLM)         | OpenAI API / Google Gemini API              |
| ML Engine        | ONNX Runtime (onnxruntime-node)             |
| ML Training      | Python + Scikit-learn (Colab/Notebook)      |
| Runtime          | Bun                                         |

---

# Komunikasi Antar Komponen

| Dari            | Ke              | Fungsi                               |
|-----------------|-----------------|--------------------------------------|
| User            | Frontend        | Interaksi antarmuka                  |
| Frontend        | Backend         | REST API (HTTP JSON)                 |
| Backend         | MySQL/MariaDB   | CRUD Data via Prisma ORM             |
| Backend         | AI Service      | Generate narasi via LLM API          |
| AI Service      | Backend         | Hasil narasi                         |
| Backend         | ML Engine       | Load model → inference               |
| ML Engine       | Backend         | Hasil prediksi (label, score)        |
| Backend         | Frontend        | Response JSON                        |
| Frontend        | User            | Menampilkan hasil                    |

---

# Environment Variables

| Variable               | Deskripsi                               |
|------------------------|-----------------------------------------|
| DATABASE_URL           | Koneksi MySQL/MariaDB                   |
| JWT_SECRET             | Secret key untuk JWT                    |
| JWT_EXPIRES_IN         | Masa berlaku token (contoh: 7d)         |
| LLM_API_KEY            | API Key LLM (OpenAI/Gemini)             |
| LLM_MODEL              | Model LLM yang digunakan                |
| NEXT_PUBLIC_API_URL    | URL backend untuk frontend              |
| ML_MODEL_PATH          | Path ke model ONNX                      |
| ML_ENABLED             | Aktifkan/nonaktifkan ML engine          |

---

# Batasan MVP

Arsitektur MVP tidak mencakup:

- Microservices
- Docker / Kubernetes
- Redis / Message Queue
- API Gateway / Load Balancer
- CI/CD Pipeline
- Monitoring Server
- Object Storage
- OCR / Document Scanning
- Integrasi Dapodik
- Integrasi Sistem Pemerintah

Pendekatan ini dipilih agar pengembangan tetap sederhana, realistis, dan sesuai dengan ruang lingkup lomba.

---

# Pengembangan Lanjutan (Non-MVP)

Apabila sistem dikembangkan lebih lanjut, beberapa komponen dapat ditambahkan.

## Artificial Intelligence

- Chatbot AI
- Retrieval-Augmented Generation (RAG)
- Vector Database
- Prediksi Prestasi Siswa
- Early Warning System

## Integrasi Sistem

- Integrasi Dapodik
- Sinkronisasi data sekolah
- Import dan Export data akademik

## Dashboard

- Dashboard Orang Tua
- Dashboard Analitik Akademik

## Otomasi

- Notifikasi Otomatis
- Reminder Administrasi
- Laporan Otomatis

## Infrastruktur

- Docker
- CI/CD
- Cloud Deployment
- Backup Database
- Monitoring Server

---

# Kesimpulan

System Architecture dirancang menggunakan pendekatan monolitik client-server sederhana: satu frontend (Next.js), satu backend (Elysia/Bun), satu database (MySQL via Prisma), dan satu layanan Artificial Intelligence (LLM API eksternal). Pendekatan ini cukup untuk mendukung seluruh kebutuhan Minimum Viable Product (MVP), sekaligus menyediakan fondasi yang mudah dikembangkan pada tahap berikutnya tanpa mengubah arsitektur utama sistem.
