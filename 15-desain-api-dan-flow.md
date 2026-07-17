# API Design & Backend Logic
## Longitudinal Student Academic Record (LSAR) — LIDM 2026

Disusun berdasarkan dokumen perencanaan resmi: problem-analysis, functional-requirement, database-schema, system-architecture, ai-system-design.

---

## 1. Konteks Singkat

**Masalah inti:** administrasi akademik SD masih manual, data siswa tersebar di banyak dokumen, tiap pergantian wali kelas guru baru butuh waktu memahami siswa, dan penyusunan Buku Induk memakan waktu karena data harus disalin manual.

**Solusi:** sistem pencatat riwayat akademik siswa per semester (longitudinal), dengan AI sebagai *decision support* (bukan pengambil keputusan) untuk membuat ringkasan/draft narasi.

**Status implementasi:** tahap Development — backend menggunakan Elysia (Bun), ORM Prisma, database MySQL/MariaDB.

---

## 2. Tech Stack

| Layer     | Teknologi                         |
|-----------|-----------------------------------|
| Frontend  | Next.js 16 + Tailwind CSS v4      |
| Backend   | **Elysia** (Bun web framework)    |
| Bahasa    | TypeScript                        |
| ORM       | Prisma                            |
| Database  | MySQL / MariaDB                   |
| Auth      | JWT (access + refresh token)      |
| AI        | LLM API (OpenAI / Google Gemini)  |

Arsitektur: **monolitik client-server sederhana** — 1 frontend, 1 backend, 1 database, 1 AI service eksternal. MVP tidak memakai microservices, Docker, Redis, message queue, atau CI/CD.

---

## 3. Database (Prisma Schema)

```
User ──┬──→ Class (homeroomTeacherId)
       └──→ SemesterRecord (createdById)
       └──→ ClassAuditLog (changedById)

AcademicYear ──┬──→ Class
               └──→ SemesterRecord

Class ──┬──→ Student
        └──→ ClassAuditLog

Student ──→ SemesterRecord

SemesterRecord ──┬──→ SubjectScore   (1:N)
                 ├──→ Attendance     (1:1 — upsert)
                 ├──→ Achievement    (1:N)
                 ├──→ HealthRecord   (1:1 — upsert)
                 └──→ AiSummary      (1:N)
```

**Aturan penting untuk backend logic:**

1. `SemesterRecord` adalah tabel pusat — semua data akademik (nilai, kehadiran, prestasi, kesehatan, ringkasan AI) menempel via `semesterRecordId`.
2. Hak akses guru ditentukan oleh `Class.homeroomTeacherId` — middleware di setiap endpoint siswa & record harus cek: apakah `user.id === class.homeroomTeacherId` untuk tahun ajaran aktif.
3. `AiSummary` hanya menyimpan **output**, bukan sumber data baru — AI service **read-only** terhadap data akademik lain, hanya **write** ke tabel ini.
4. `Attendance` dan `HealthRecord` bersifat 1:1 — gunakan Prisma `upsert`, bukan `create` berulang.
5. `SubjectScore` memiliki constraint `@@unique([semesterRecordId, subjectName])` — gunakan `upsert` juga.
6. `SemesterRecord` memiliki constraint `@@unique([studentId, academicYearId, semester])` — cek sebelum create.

---

## 4. Aktor & Role

| Role             | Hak Akses Utama                                                    |
|------------------|--------------------------------------------------------------------|
| ADMINISTRATOR    | Kelola user, tahun ajaran, kelas, teacher assignment               |
| OPERATOR_SEKOLAH | Kelola data siswa, perpindahan siswa                               |
| GURU             | Kelola data akademik siswa di kelasnya, AI, Preview Buku Induk     |
| KEPALA_SEKOLAH   | Monitoring (read-only) seluruh data akademik                       |

> Catatan: akun **Guru** dan **Wali Kelas** adalah role yang sama — akses ke siswa ditentukan oleh Teacher Assignment di tabel Class.

---

## 5. Konvensi API

### Base URL

```
http://localhost:3000/api
```

### Response Format

Semua response menggunakan format JSON seragam:

**Success (200/201):**
```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 100
  }
}
```

**Error (4xx/5xx):**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Deskripsi error yang jelas"
  }
}
```

### Kode Error Standar

| Kode                   | HTTP Status | Deskripsi                           |
|------------------------|-------------|-------------------------------------|
| VALIDATION_ERROR       | 400         | Data input tidak valid              |
| UNAUTHORIZED           | 401         | Token tidak valid / expired         |
| FORBIDDEN              | 403         | Tidak memiliki hak akses            |
| NOT_FOUND              | 404         | Resource tidak ditemukan            |
| CONFLICT               | 409         | Duplikasi data (constraint unique)  |
| AI_ERROR               | 502         | Gagal memanggil LLM API             |
| INTERNAL_ERROR         | 500         | Error server internal               |

### Pagination

Endpoint yang mengembalikan daftar resource menggunakan query parameter:
- `?page=1` (default: 1)
- `?limit=20` (default: 20, max: 100)

Response menyertakan `meta` dengan `page`, `limit`, `total`.

### Autentikasi

- Kirim JWT via header: `Authorization: Bearer <token>`
- Access token: masa berlaku pendek (15 menit - 24 jam, sesuai kebutuhan)
- Refresh token: masa berlaku lebih panjang (7 hari)

---

## 6. Modul Backend & Business Logic

### 6.1 Authentication & Authorization (FR-01)

- Login → validasi username & password (hashed) → return JWT
- Middleware `requireAuth` untuk verifikasi token
- Middleware `requireRole(...)` untuk endpoint admin/operator
- Middleware `requireHomeroomAccess(studentId)` untuk endpoint guru — validasi via join `Student → Class → homeroomTeacherId`

### 6.2 Academic Year Management (FR-02)

- Hanya 1 AcademicYear yang boleh `isActive = true` dalam satu waktu
- Saat mengaktifkan tahun ajaran baru, backend otomatis menonaktifkan yang lama (transaksi atomik via Prisma)
- Archive = soft flag (`isArchived = true`), bukan hapus data

### 6.3 Teacher Assignment (FR-03)

- Update `Class.homeroomTeacherId` untuk mengubah wali kelas
- Setiap perubahan dicatat di `ClassAuditLog` (otomatis via backend)
- Perubahan ini langsung memengaruhi hak akses guru

### 6.4 Student Management (FR-04)

- CRUD dasar dengan validasi NIS/NISN unik
- `classId` wajib untuk menentukan hak akses guru

### 6.5 Semester Record & Academic Data (FR-05)

- Satu `SemesterRecord` dibuat per kombinasi `(studentId, academicYearId, semester)` — unique constraint
- Sub-resource (nilai, kehadiran, prestasi, kesehatan) nested di bawah `semesterRecordId`
- **SubjectScore** menggunakan `knowledgeScore` dan `skillsScore` terpisah (sesuai Kurikulum Merdeka)
- **Attendance** dan **HealthRecord** menggunakan upsert

### 6.6 Longitudinal Profile & Timeline (FR-06, FR-07)

- Semua `SemesterRecord` milik satu siswa, diurutkan berdasarkan tahun ajaran & semester
- Digunakan untuk tampilan timeline kelas 1–6

### 6.7 Administrative Workspace & Buku Induk (FR-08, FR-09, FR-10)

- **Validation**: cek apakah SubjectScore, Attendance, HealthRecord sudah terisi untuk setiap `SemesterRecord` → kembalikan daftar field yang belum lengkap
- **Buku Induk Preview**: read-only, format ulang data yang sudah ada, **tidak** generate dokumen digital

### 6.8 AI Features (FR-11, FR-12, FR-13)

Alur:
```
Guru pilih siswa
  → Backend ambil data akademik (nilai, kehadiran, prestasi, kesehatan)
  → Backend susun prompt terstruktur
  → Kirim ke LLM API (OpenAI/Gemini)
  → Terima narasi
  → Simpan sebagai draft di AiSummary (isFinal = false)
  → Guru review → edit / terima / regenerate
  → Guru simpan versi final (isFinal = true)
```

- **Human-in-the-loop wajib**: hasil AI tidak pernah langsung dianggap final
- **Versioning**: setiap regenerate menambah `version` — guru bisa bandingkan draft
- AI **tidak mengubah** data akademik sumber — hanya CREATE ke `AiSummary`

### 6.9 Dashboard (FR-14)

- Agregasi sederhana (count query) — tidak perlu tabel khusus di MVP

---

## 7. Daftar Lengkap API Endpoint

Semua endpoint diawali `/api`.

### Auth

| Method | Endpoint                | Role              | Keterangan                    |
|--------|-------------------------|-------------------|-------------------------------|
| POST   | `/auth/login`           | Semua             | Return JWT access + refresh   |
| POST   | `/auth/refresh`         | Semua             | Refresh access token          |
| POST   | `/auth/logout`          | Semua             | Invalidasi token              |
| GET    | `/auth/me`              | Semua             | Data user login + role        |

### User Management (FR-01)

| Method | Endpoint                | Role              |
|--------|-------------------------|-------------------|
| GET    | `/users`                | ADMINISTRATOR     |
| POST   | `/users`                | ADMINISTRATOR     |
| GET    | `/users/:id`            | ADMINISTRATOR     |
| PUT    | `/users/:id`            | ADMINISTRATOR     |
| PATCH  | `/users/:id/status`     | ADMINISTRATOR     |

### Academic Year (FR-02)

| Method | Endpoint                       | Role              |
|--------|--------------------------------|-------------------|
| GET    | `/academic-years`              | ADMINISTRATOR     |
| POST   | `/academic-years`              | ADMINISTRATOR     |
| PUT    | `/academic-years/:id`          | ADMINISTRATOR     |
| PATCH  | `/academic-years/:id/activate` | ADMINISTRATOR     |
| PATCH  | `/academic-years/:id/archive`  | ADMINISTRATOR     |

### Class & Teacher Assignment (FR-03)

| Method | Endpoint                              | Role                                    |
|--------|---------------------------------------|-----------------------------------------|
| GET    | `/classes`                            | ADMINISTRATOR, OPERATOR_SEKOLAH, KEPSEK |
| POST   | `/classes`                            | ADMINISTRATOR                           |
| PUT    | `/classes/:id`                        | ADMINISTRATOR                           |
| PATCH  | `/classes/:id/homeroom-teacher`       | ADMINISTRATOR                           |
| GET    | `/classes/:id/students`               | ADMINISTRATOR, OPERATOR, GURU, KEPSEK   |

### Student (FR-04)

| Method | Endpoint                | Role                                    |
|--------|-------------------------|-----------------------------------------|
| GET    | `/students`             | OPERATOR_SEKOLAH, KEPSEK                |
| POST   | `/students`             | OPERATOR_SEKOLAH                        |
| GET    | `/students/:id`         | OPERATOR, GURU (kelasnya), KEPSEK       |
| PUT    | `/students/:id`         | OPERATOR_SEKOLAH                        |
| GET    | `/students?classId=:id` | OPERATOR, GURU, KEPSEK                  |

### Semester Record & Sub-resource (FR-05)

| Method | Endpoint                                           | Role          |
|--------|----------------------------------------------------|---------------|
| POST   | `/students/:id/semester-records`                   | GURU          |
| GET    | `/semester-records/:id`                            | GURU, KEPSEK  |
| PUT    | `/semester-records/:id`                            | GURU          |
| DELETE | `/semester-records/:id`                            | GURU          |
| POST   | `/semester-records/:id/subject-scores`             | GURU          |
| PUT    | `/semester-records/:id/subject-scores/:scoreId`    | GURU          |
| DELETE | `/semester-records/:id/subject-scores/:scoreId`    | GURU          |
| PUT    | `/semester-records/:id/attendance`                 | GURU (upsert) |
| POST   | `/semester-records/:id/achievements`               | GURU          |
| PUT    | `/achievements/:id`                                | GURU          |
| DELETE | `/achievements/:id`                                | GURU          |
| PUT    | `/semester-records/:id/health-record`              | GURU (upsert) |

### Longitudinal Profile & Timeline (FR-06, FR-07)

| Method | Endpoint                                          | Role          |
|--------|---------------------------------------------------|---------------|
| GET    | `/students/:id/profile`                           | GURU, KEPSEK  |
| GET    | `/students/:id/timeline`                          | GURU, KEPSEK  |
| GET    | `/students/:id/semester-records`                  | GURU, KEPSEK  |

### Administrative Workspace / Buku Induk (FR-08, FR-09, FR-10)

| Method | Endpoint                                | Role          |
|--------|-----------------------------------------|---------------|
| GET    | `/students/:id/administrative-workspace`| GURU          |
| GET    | `/students/:id/validation-status`       | GURU          |
| GET    | `/students/:id/buku-induk-preview`      | GURU, KEPSEK  |

### AI Features (FR-11, FR-12, FR-13)

| Method | Endpoint                                    | Role |
|--------|---------------------------------------------|------|
| POST   | `/ai/students/:id/summary`                  | GURU |
| POST   | `/ai/students/:id/draft-description`        | GURU |
| POST   | `/ai/classes/:id/transition-summary`        | GURU |
| GET    | `/semester-records/:id/ai-summaries`        | GURU, KEPSEK |
| PUT    | `/ai-summaries/:id`                         | GURU (revisi) |
| POST   | `/ai-summaries/:id/regenerate`              | GURU |
| DELETE | `/ai-summaries/:id`                         | GURU |

### Dashboard (FR-14)

| Method | Endpoint                           | Role              |
|--------|------------------------------------|-------------------|
| GET    | `/dashboard/summary`               | Semua (by role)   |
| GET    | `/dashboard/administrative-status` | GURU, KEPSEK      |

---

## 8. Middleware & Authorization Flow

### Homeroom Access Check

```
Request → Authenticate (JWT) → Check Role → [If GURU] Check Homeroom:
  1. Get student by ID → ambil classId
  2. Get class → cek homeroomTeacherId === userId
  3. Jika tidak cocok → return 403 FORBIDDEN
```

### Academic Year Context

Semua operasi GURU secara default menggunakan tahun ajaran yang aktif (`AcademicYear.isActive = true`).

---

## 9. Error Handling Strategy

### Prisma Error Mapping

| Prisma Error             | API Error          |
|--------------------------|--------------------|
| `P2002` (Unique violation)| CONFLICT (409)     |
| `P2025` (Record not found)| NOT_FOUND (404)    |
| `P2003` (FK violation)   | VALIDATION_ERROR (400) |

### LLM API Error Handling

- Timeout: retry 1x, jika gagal → AI_ERROR (502)
- Rate limit: queue atau beri jeda, informasikan ke user
- Invalid response: validasi format output, minta regenerate

---

## 10. Yang Sengaja Tidak Ada di MVP

Sesuai batasan eksplisit di dokumen (jangan dibangun dulu):

- Chatbot AI, RAG, Vector Database
- Prediksi prestasi siswa, Early Warning System
- Dashboard orang tua
- Notifikasi otomatis
- Integrasi Dapodik / sistem pemerintah
- Microservices, Docker, Kubernetes, Redis, Message Queue, CI/CD

---

## 11. Rekomendasi Implementasi

1. **Error handling global**: buat Elysia plugin/error handler untuk menangkap semua error dan mengembalikan format seragam.
2. **Rate limiting** untuk endpoint AI (`/ai/*`) karena memanggil LLM API eksternal berbayar.
3. **Audit log** untuk perubahan `homeroomTeacherId` sudah diakomodasi oleh model `ClassAuditLog`.
4. **Versioning draft AI**: field `version` dan `isFinal` di `AiSummary` memungkinkan perbandingan draft.
5. **Transaksi Prisma**: gunakan `prisma.$transaction()` untuk operasi yang mengubah beberapa tabel sekaligus.
6. **Upsert pattern**: untuk Attendance, HealthRecord, dan SubjectScore, gunakan Prisma `upsert` untuk menghindari duplikasi.
