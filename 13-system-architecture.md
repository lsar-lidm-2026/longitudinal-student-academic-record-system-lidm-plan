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
                           Guru
                             │
                             │
                    Web Browser
                             │
                             ▼
                  Frontend (React.js)
                             │
                       REST API (HTTPS)
                             │
                             ▼
                  Backend (FastAPI)
          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼
   PostgreSQL          AI Service         Authentication
   Database           (LLM API)           Authorization
          │                  │
          └──────────┬───────┘
                     ▼
               Response ke Guru
```

---

# Komponen MVP

## 1. User

Pengguna sistem terdiri dari:

- Administrator
- Operator Sekolah
- Wali Kelas
- Kepala Sekolah

Seluruh pengguna mengakses sistem melalui browser.

---

## 2. Frontend

Frontend merupakan antarmuka yang digunakan oleh pengguna.

Teknologi

- React.js
- Tailwind CSS

Fungsi

- Login
- Dashboard
- Data Siswa
- Riwayat Akademik
- Input Semester
- AI Assistant
- Preview Buku Induk

Frontend hanya menampilkan data dan mengirim permintaan ke backend.

---

## 3. Backend

Backend bertanggung jawab menjalankan seluruh logika bisnis sistem.

Teknologi

- FastAPI

Fungsi

- Autentikasi pengguna
- Validasi data
- Mengelola data siswa
- Mengelola nilai
- Mengelola kehadiran
- Mengelola prestasi
- Mengelola riwayat akademik
- Berkomunikasi dengan AI Service
- Mengakses database

---

## 4. Database

Database menyimpan seluruh data sistem.

Teknologi

- PostgreSQL

Data yang disimpan

- Pengguna
- Siswa
- Kelas
- Tahun Ajaran
- Nilai
- Kehadiran
- Prestasi
- Data Kesehatan
- Catatan Guru
- Ringkasan AI

Database menjadi sumber utama informasi akademik siswa.

---

## 5. AI Service

Artificial Intelligence digunakan sebagai asisten guru.

Teknologi

- Large Language Model (LLM API)

Fungsi

- Generate Student Summary
- Generate Draft Deskripsi Rapor
- Generate Student Transition Summary

AI hanya menerima data dari backend dan menghasilkan narasi.

AI tidak mengubah data akademik siswa.

---

# Alur Kerja Sistem

## Login

User

↓

Frontend

↓

Backend

↓

Database

↓

Validasi

↓

Dashboard

---

## Input Data Akademik

Guru

↓

Frontend

↓

Backend

↓

Database

↓

Data Tersimpan

---

## Generate AI

Guru

↓

Frontend

↓

Backend

↓

Database

↓

Mengambil Riwayat Akademik

↓

AI Service

↓

Generate Narasi

↓

Backend

↓

Frontend

↓

Guru Review

---

## Preview Buku Induk

Guru

↓

Frontend

↓

Backend

↓

Database

↓

Menyusun Data

↓

Preview

↓

Guru Menyalin ke Buku Induk Manual

---

# Hak Akses

## Administrator

- Kelola pengguna
- Kelola kelas
- Kelola tahun ajaran

---

## Operator Sekolah

- Kelola data siswa
- Kelola perpindahan siswa

---

## Wali Kelas

- Mengelola data akademik
- Melihat riwayat siswa
- Menggunakan AI Assistant
- Melihat Preview Buku Induk

---

## Kepala Sekolah

- Monitoring data akademik
- Melihat riwayat siswa
- Melihat Preview Buku Induk

---

# Teknologi yang Digunakan (MVP)

| Komponen | Teknologi |
|----------|-----------|
| Frontend | React.js |
| Styling | Tailwind CSS |
| Backend | FastAPI |
| Database | PostgreSQL |
| ORM | SQLAlchemy |
| Authentication | JWT |
| AI | OpenAI API / Google Gemini API |

---

# Komunikasi Antar Komponen

| Dari | Ke | Fungsi |
|------|----|--------|
| User | Frontend | Interaksi sistem |
| Frontend | Backend | REST API |
| Backend | PostgreSQL | CRUD Data |
| Backend | AI Service | Generate Narasi |
| AI Service | Backend | Hasil AI |
| Backend | Frontend | Response |
| Frontend | User | Menampilkan hasil |

---

# Batasan MVP

Arsitektur MVP tidak mencakup:

- Microservices
- Docker
- Kubernetes
- Redis
- Message Queue
- API Gateway
- Load Balancer
- CI/CD Pipeline
- Monitoring Server
- Multi Database
- Object Storage
- OCR
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

---

## Integrasi Sistem

- Integrasi Dapodik
- Sinkronisasi data sekolah
- Import dan Export data akademik

---

## Dashboard

- Dashboard Orang Tua
- Dashboard Kepala Sekolah
- Dashboard Analitik Akademik

---

## Otomasi

- Notifikasi Otomatis
- Reminder Administrasi
- Laporan Otomatis

---

## Infrastruktur

- Docker
- CI/CD
- Cloud Deployment
- Backup Database
- Monitoring Server

---

# Kesimpulan

System Architecture dirancang menggunakan pendekatan client-server sederhana dengan satu frontend, satu backend, satu database, dan satu layanan Artificial Intelligence. Pendekatan ini cukup untuk mendukung seluruh kebutuhan Minimum Viable Product (MVP), sekaligus menyediakan fondasi yang mudah dikembangkan pada tahap berikutnya tanpa mengubah arsitektur utama sistem.