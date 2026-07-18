# Implementation Roadmap
## Longitudinal Student Academic Record (LSAR) — LIDM 2026

# Pendahuluan

Dokumen ini berisi rencana implementasi sistem secara bertahap (sprint-based) untuk memastikan pengembangan berjalan terstruktur, terukur, dan selesai tepat waktu untuk kompetisi LIDM 2026.

Setiap sprint memiliki durasi 1 minggu (7 hari) dengan target deliverable yang jelas.

---

# Prioritas Pengembangan

| Prioritas | Area                      | Alasan                                                                     |
|-----------|---------------------------|----------------------------------------------------------------------------|
| P0        | Core Infrastructure       | Tanpa ini, sistem tidak bisa berjalan (auth, database, routing)            |
| P1        | Master Data Management    | Data master diperlukan sebelum fitur akademik bisa bekerja                  |
| P2        | Academic Features         | Fitur inti longitudinal academic record                                     |
| P3        | AI Features               | Fitur AI (LLM) sebagai nilai tambah sistem                                 |
| P4        | Admin & Workspace         | Dashboard, buku induk, validasi administrasi                               |
| P5        | ML Features               | Rule-based alert, prediksi tren, early warning system (post-MVP)           |
| P6        | Refinement & Polish       | Perbaikan UI/UX, bug fixing, persiapan demo                                |

---

# Sprint Plan

## Sprint 1: Foundation (Minggu 1)
**Fokus: Setup proyek, database, dan infrastruktur dasar**

| Task ID | Task                         | Priority | Module       | Estimasi |
|---------|------------------------------|----------|--------------|----------|
| S1-T1   | Inisialisasi project backend | P0       | Backend      | 1 hari   |
| S1-T2   | Setup Prisma & database      | P0       | Backend      | 1 hari   |
| S1-T3   | Setup project frontend       | P0       | Frontend     | 1 hari   |
| S1-T4   | Implementasi schema Prisma   | P0       | Backend      | 2 hari   |
| S1-T5   | Setup routing Elysia         | P0       | Backend      | 1 hari   |
| S1-T6   | Setup Tailwind & layout dasar| P0       | Frontend     | 1 hari   |

**Deliverable:**
- Backend Elysia + Prisma dapat berjalan dengan Bun
- Database PostgreSQL terhubung
- Semua model Prisma sudah di-migrate
- Frontend Next.js dapat berjalan
- Layout dasar halaman sudah siap

---

## Sprint 2: Authentication & Authorization (Minggu 2)
**Fokus: Login, register, JWT, middleware role**

| Task ID | Task                             | Priority | Module       | Estimasi |
|---------|----------------------------------|----------|--------------|----------|
| S2-T1   | Implementasi login endpoint      | P0       | Backend      | 1 hari   |
| S2-T2   | JWT access + refresh token       | P0       | Backend      | 1 hari   |
| S2-T3   | Middleware requireAuth            | P0       | Backend      | 1 hari   |
| S2-T4   | Middleware requireRole            | P0       | Backend      | 1 hari   |
| S2-T5   | Seeder data user awal            | P0       | Backend      | 0.5 hari |
| S2-T6   | Halaman login frontend           | P0       | Frontend     | 1 hari   |
| S2-T7   | Auth context & protected routes   | P0       | Frontend     | 1 hari   |
| S2-T8   | Halaman dashboard statis         | P1       | Frontend     | 1 hari   |

**Deliverable:**
- Login/logout flow lengkap (FE + BE)
- JWT authentication dengan refresh token
- Role-based middleware berfungsi
- Halaman login dengan validasi
- Dashboard dasar dengan data user

---

## Sprint 3: Master Data (Minggu 3)
**Fokus: CRUD Academic Year, Class, Student, Teacher Assignment**

| Task ID | Task                                    | Priority | Module       | Estimasi |
|---------|-----------------------------------------|----------|--------------|----------|
| S3-T1   | CRUD Academic Year (BE)                 | P1       | Backend      | 1 hari   |
| S3-T2   | CRUD Class + Teacher Assignment (BE)    | P1       | Backend      | 1 hari   |
| S3-T3   | CRUD Student (BE)                       | P1       | Backend      | 1 hari   |
| S3-T4   | Audit log ClassAuditLog (BE)            | P1       | Backend      | 0.5 hari |
| S3-T5   | Halaman manajemen tahun ajaran (FE)     | P1       | Frontend     | 1 hari   |
| S3-T6   | Halaman manajemen kelas (FE)            | P1       | Frontend     | 1 hari   |
| S3-T7   | Halaman manajemen siswa (FE)            | P1       | Frontend     | 1 hari   |
| S3-T8   | Assign wali kelas UI (FE)               | P1       | Frontend     | 1 hari   |

**Deliverable:**
- CRUD lengkap Academic Year, Class, Student
- Teacher Assignment dengan audit log
- UI admin/operator untuk master data

---

## Sprint 4: Academic Record (Minggu 4)
**Fokus: CRUD Semester Record, nilai, kehadiran, prestasi, kesehatan**

| Task ID | Task                                              | Priority | Module       | Estimasi |
|---------|---------------------------------------------------|----------|--------------|----------|
| S4-T1   | CRUD SemesterRecord (BE)                          | P2       | Backend      | 1 hari   |
| S4-T2   | CRUD SubjectScore + upsert logic (BE)             | P2       | Backend      | 1 hari   |
| S4-T3   | CRUD Attendance + upsert (BE)                     | P2       | Backend      | 1 hari   |
| S4-T4   | CRUD Achievement (BE)                             | P2       | Backend      | 0.5 hari |
| S4-T5   | CRUD HealthRecord + upsert (BE)                   | P2       | Backend      | 0.5 hari |
| S4-T6   | Middleware homeroom access check (BE)             | P2       | Backend      | 1 hari   |
| S4-T7   | Halaman input semester (FE)                       | P2       | Frontend     | 2 hari   |
| S4-T8   | Tab nilai, kehadiran, prestasi, kesehatan (FE)    | P2       | Frontend     | 1 hari   |

**Deliverable:**
- Guru dapat membuat semester record untuk siswa
- Input nilai (knowledge + skills), kehadiran, prestasi, kesehatan
- Upsert logic untuk data 1:1
- Homeroom access check berfungsi

---

## Sprint 5: Longitudinal Profile & Timeline (Minggu 5)
**Fokus: Menampilkan riwayat longitudinal siswa**

| Task ID | Task                                         | Priority | Module       | Estimasi |
|---------|----------------------------------------------|----------|--------------|----------|
| S5-T1   | Endpoint profile siswa lengkap (BE)          | P2       | Backend      | 1 hari   |
| S5-T2   | Endpoint timeline longitudinal (BE)          | P2       | Backend      | 1 hari   |
| S5-T3   | Halaman detail siswa (FE)                    | P2       | Frontend     | 2 hari   |
| S5-T4   | Komponen timeline semester (FE)              | P2       | Frontend     | 1 hari   |
| S5-T5   | Filter semester & navigasi timeline (FE)     | P2       | Frontend     | 1 hari   |

**Deliverable:**
- Halaman profil siswa dengan biodata
- Timeline longitudinal kelas 1-6
- Guru dapat memilih semester dan melihat detail

---

## Sprint 6: AI Features (Minggu 6)
**Fokus: Integrasi LLM, Student Summary, Draft Deskripsi, Transition Summary**

| Task ID | Task                                         | Priority | Module       | Estimasi |
|---------|----------------------------------------------|----------|--------------|----------|
| S6-T1   | Service LLM client (BE)                      | P3       | Backend      | 1 hari   |
| S6-T2   | Prompt engineering untuk 3 fitur AI (BE)     | P3       | Backend      | 1 hari   |
| S6-T3   | Endpoint AI Student Summary (BE)             | P3       | Backend      | 1 hari   |
| S6-T4   | Endpoint AI Draft Deskripsi (BE)             | P3       | Backend      | 1 hari   |
| S6-T5   | Endpoint AI Transition Summary (BE)          | P3       | Backend      | 1 hari   |
| S6-T6   | Versioning & regenerate logic (BE)           | P3       | Backend      | 1 hari   |
| S6-T7   | Halaman AI Assistant (FE)                    | P3       | Frontend     | 2 hari   |
| S6-T8   | Review, edit, regenerate UI (FE)             | P3       | Frontend     | 1 hari   |

**Deliverable:**
- 3 fitur AI berfungsi penuh
- Human-in-the-loop: review → edit → finalisasi
- Versioning draft (regenerate = version++)
- Loading state & error handling untuk LLM call

---

## Sprint 7: Buku Induk & Administration (Minggu 7)
**Fokus: Preview Buku Induk, validasi administrasi, workspace**

| Task ID | Task                                           | Priority | Module       | Estimasi |
|---------|------------------------------------------------|----------|--------------|----------|
| S7-T1   | Endpoint administrative workspace (BE)         | P4       | Backend      | 1 hari   |
| S7-T2   | Endpoint validation status (BE)                | P4       | Backend      | 1 hari   |
| S7-T3   | Endpoint buku induk preview (BE)              | P4       | Backend      | 1 hari   |
| S7-T4   | Halaman administrative workspace (FE)          | P4       | Frontend     | 1 hari   |
| S7-T5   | Komponen validation status (FE)                | P4       | Frontend     | 1 hari   |
| S7-T6   | Halaman preview buku induk (FE)               | P4       | Frontend     | 2 hari   |
| S7-T7   | Dashboard dengan widget progress (FE)          | P4       | Frontend     | 1 hari   |

**Deliverable:**
- Preview Buku Induk menampilkan data siap salin
- Status kelengkapan data per semester
- Administrative Preparation Workspace
- Dashboard dengan data real

---

## Sprint 8: ML Features (Minggu 8)
**Fokus: Rule-based early warning, trend analysis, ML infrastructure**

| Task ID | Task                                                | Priority | Module       | Estimasi |
|---------|-----------------------------------------------------|----------|--------------|----------|
| S8-T1   | Feature engineering queries (BE)                    | P5       | Backend      | 1 hari   |
| S8-T2   | Rule-based early warning system (BE)                | P5       | Backend      | 1 hari   |
| S8-T3   | Trend analysis endpoint (BE)                        | P5       | Backend      | 1 hari   |
| S8-T4   | Endpoint ML model management (BE)                   | P5       | Backend      | 1 hari   |
| S8-T5   | Migration PredictedOutcome & MlModel (DB)           | P5       | Backend      | 0.5 hari |
| S8-T6   | Dashboard analitik: grafik tren (FE)                | P5       | Frontend     | 1 hari   |
| S8-T7   | Heatmap risiko per kelas (FE)                       | P5       | Frontend     | 1 hari   |

**Deliverable:**
- Rule-based early warning (Aman/Waspada/Kritis)
- Grafik tren nilai per siswa
- Heatmap risiko per kelas
- Tabel PredictedOutcome dan MlModel siap

---

## Sprint 9: Polish & Demo Preparation (Minggu 9)
**Fokus: Bug fixing, UI polish, data seeder demo, persiapan presentasi**

| Task ID | Task                                        | Priority | Module       | Estimasi |
|---------|---------------------------------------------|----------|--------------|----------|
| S8-T1   | Data seeder demonstrasi (BE)                | P5       | Backend      | 1 hari   |
| S8-T2   | Error handling & edge cases (BE)            | P5       | Backend      | 1 hari   |
| S8-T3   | UI polish & responsive design (FE)          | P5       | Frontend     | 2 hari   |
| S8-T4   | Loading state & empty state (FE)            | P5       | Frontend     | 1 hari   |
| S8-T5   | Testing alur utama (manual)                 | P5       | Both         | 1 hari   |
| S8-T6   | Persiapan materi demo & dokumentasi         | P5       | Documentation| 1 hari   |

**Deliverable:**
- Aplikasi siap demo dengan data contoh
- Semua edge case ter-handle
- UI responsif dan rapi
- Materi presentasi siap

---

# Milestone Summary

| Milestone | Sprint | Target Selesai   | Capaian                                                   |
|-----------|--------|------------------|-----------------------------------------------------------|
| M1        | 1-2    | Akhir Minggu 2   | Foundation + Auth: sistem dapat digunakan dan login        |
| M2        | 3      | Akhir Minggu 3   | Master Data: admin dapat mengelola data master             |
| M3        | 4-5    | Akhir Minggu 5   | Academic Record: guru dapat input & lihat riwayat siswa   |
| M4        | 6      | Akhir Minggu 6   | AI Features: 3 fitur AI berfungsi                          |
| M5        | 7      | Akhir Minggu 7   | Administration: preview buku induk & validasi siap         |
| M6        | 8      | Akhir Minggu 8   | ML Features: early warning, trend analysis, notebook       |
| M7        | 9      | Akhir Minggu 9   | Final: aplikasi siap demo untuk LIDM 2026                  |

---

# Dependencies Antar Task

```text
Sprint 1 (Foundation)
  └── Sprint 2 (Auth) ── bergantung pada database & routing
        └── Sprint 3 (Master Data) ── bergantung pada auth
              └── Sprint 4 (Academic Record) ── bergantung pada student & class
                    ├── Sprint 5 (Timeline) ── bergantung pada semester record
                    ├── Sprint 6 (AI/LLM) ── bergantung pada data akademik
                    │     └── Sprint 8 (ML) ── bergantung pada data akademik longitudinal
                    └── Sprint 7 (Buku Induk) ── bergantung pada semua data
                          └── Sprint 9 (Polish) ── bergantung pada seluruh fitur
```

---

# Teknologi & Tools

| Tool          | Kegunaan                               |
|---------------|----------------------------------------|
| Bun           | Runtime JavaScript/TypeScript          |
| Elysia        | Web framework backend                  |
| Prisma        | ORM & database migration               |
| Next.js 16    | Frontend framework                     |
| Tailwind CSS 4| Utility-first CSS                      |
| Git           | Version control                        |
| GitHub        | Repository & collaboration             |
| PostgreSQL | Database                               |

---

# Risiko & Mitigasi

| Risiko                                      | Dampak           | Mitigasi                                        |
|---------------------------------------------|------------------|-------------------------------------------------|
| Keterlambatan setup environment             | Sprint 1 molor   | Dokumentasi setup jelas, template project siap  |
| API LLM berbayar melebihi kuota            | Fitur AI terhambat| Gunakan model gratis (Gemini) untuk development |
| Perubahan requirement dari dosen pembimbing | Scope bertambah  | Catat perubahan, evaluasi dampak sprint         |
| Anggota tim sakit/bentrok jadwal            | Kapasitas turun  | Pembagian task jelas, daily check-in            |
| Bug integrasi FE-BE                         | Sprint molor     | Contract API didefinisikan sebelum implementasi  |
| Database migration conflict                 | Data hilang      | Version control untuk migration, backup sebelum |
