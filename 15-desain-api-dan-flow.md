# API Design & Backend Logic
## Longitudinal Student Academic Record (LSAR) — LIDM 2026

Disusun berdasarkan dokumen perencanaan resmi di repo:
`lsar-lidm-2026/longitudinal-student-academic-record-system-lidm-plan`
(01-problem-analysis, 04-functional-requirement, 08-database-schema, 13-system-architecture, 14-ai-system-design)

---

## 1. Konteks Singkat

**Masalah inti:** administrasi akademik SD masih manual, data siswa tersebar di banyak dokumen, tiap pergantian wali kelas guru baru butuh waktu memahami siswa, dan penyusunan Buku Induk memakan waktu karena data harus disalin manual.

**Solusi:** sistem pencatat riwayat akademik siswa per semester (longitudinal), dengan AI sebagai *decision support* (bukan pengambil keputusan) untuk membuat ringkasan/draft narasi.

**Status:** proyek masih tahap *Planning* — belum ada kode, baru dokumen desain. Dokumen ini adalah rancangan backend & API yang bisa langsung dipakai sebagai starting point implementasi.

---

## 2. Tech Stack (sesuai 13-system-architecture.md)

| Layer | Teknologi |
|---|---|
| Frontend | React.js + Tailwind CSS |
| Backend | **FastAPI** (Python) |
| ORM | SQLAlchemy |
| Database | PostgreSQL |
| Auth | JWT |
| AI | LLM API (OpenAI API / Google Gemini API) — bukan model yang di-hosting sendiri |

Arsitektur: **monolith client-server sederhana** — 1 frontend, 1 backend, 1 database, 1 AI service eksternal. MVP sengaja **tidak** memakai microservices, Docker, Redis, message queue, API gateway, atau CI/CD (baru masuk roadmap non-MVP).

---

## 3. Rekap Database (08-database-schema.md)

```
DB_USER ──┬─→ DB_CLASS (homeroom_teacher_id)
          └─→ DB_SEMESTER_RECORD (created_by)

DB_ACADEMIC_YEAR ──┬─→ DB_CLASS
                    └─→ DB_SEMESTER_RECORD

DB_CLASS ──→ DB_STUDENT ──→ DB_SEMESTER_RECORD
                                 ├─→ DB_SUBJECT_SCORE   (1..N)
                                 ├─→ DB_ATTENDANCE       (1..1)
                                 ├─→ DB_ACHIEVEMENT      (1..N)
                                 ├─→ DB_HEALTH_RECORD    (1..1)
                                 └─→ DB_AI_SUMMARY       (1..N)
```

**Aturan penting untuk backend logic:**
- `DB_SEMESTER_RECORD` adalah tabel pusat — semua data akademik (nilai, kehadiran, prestasi, kesehatan, ringkasan AI) menempel via `record_id`.
- Hak akses guru ditentukan oleh `DB_CLASS.homeroom_teacher_id` (Teacher Assignment) — **bukan** oleh kepemilikan data langsung. Middleware/dependency di setiap endpoint siswa & record harus cek: *apakah user login = homeroom_teacher_id dari kelas siswa ini pada tahun ajaran aktif?*
- `DB_AI_SUMMARY` hanya menyimpan **output**, bukan sumber data baru — AI service **read-only** terhadap data akademik lain, hanya **write** ke tabel ini.
- `DB_ATTENDANCE` dan `DB_HEALTH_RECORD` bersifat 1:1 terhadap `record_id` (constraint UNIQUE) → gunakan `upsert`, bukan `insert` berulang.

---

## 4. Aktor & Role

| Role | Hak Akses Utama |
|---|---|
| Administrator | Kelola pengguna, kelola tahun ajaran, kelola kelas |
| Operator Sekolah | Kelola data siswa, kelola perpindahan siswa |
| Guru / Wali Kelas | Kelola data akademik siswa di kelasnya, AI Assistant, Preview Buku Induk |
| Kepala Sekolah | Monitoring (read-only) seluruh data akademik & Buku Induk |

> Catatan dari dokumen: pada MVP, akun **Guru** dan **Wali Kelas** sama — akses ditentukan oleh *Teacher Assignment*, bukan role terpisah.

---

## 5. Modul Backend & Business Logic

### 5.1 Authentication & Authorization (FR-01)
- Login → JWT (access token + refresh token).
- Middleware `require_role(...)` untuk endpoint admin/operator.
- Middleware `require_homeroom_access(student_id)` untuk endpoint guru — validasi lewat join `DB_STUDENT → DB_CLASS → homeroom_teacher_id`.

### 5.2 Academic Year Management (FR-02)
- Hanya 1 tahun ajaran yang boleh `is_active = true` dalam satu waktu → saat mengaktifkan tahun ajaran baru, backend otomatis men-nonaktifkan yang lama (transaksi atomik).
- Arsip tahun ajaran = soft flag, bukan hapus data.

### 5.3 Teacher Assignment (FR-03)
- Mengubah wali kelas cukup update `DB_CLASS.homeroom_teacher_id`. Tidak perlu tabel histori terpisah di MVP, tapi disarankan menyimpan log perubahan minimal (audit trail) karena ini berdampak langsung ke hak akses.

### 5.4 Student Management (FR-04)
- CRUD dasar. `class_id` wajib untuk menentukan hak akses guru.
- Validasi NIS/NISN unik disarankan meski tidak eksplisit di skema (tabel tidak set UNIQUE constraint pada `nis`/`nisn` — perlu diputuskan saat implementasi).

### 5.5 Student Academic Record per Semester (FR-05)
- Satu `DB_SEMESTER_RECORD` dibuat per siswa per tahun ajaran (kombinasi `student_id` + `academic_year_id` idealnya unique).
- Sub-resource (nilai, kehadiran, prestasi, kesehatan) semua menempel ke `record_id` ini — endpoint sebaiknya nested di bawah `/semester-records/{id}/...`.

### 5.6 Longitudinal Profile & Timeline (FR-06, FR-07)
- Endpoint agregasi: join semua semester record milik satu siswa, urut berdasarkan tahun ajaran & semester → dipakai untuk tampilan timeline kelas 1–6.
- Ini query gabungan (read-heavy), pertimbangkan pagination per semester jika data besar.

### 5.7 Administrative Workspace, Validation, Buku Induk Preview (FR-08, FR-09, FR-10)
- Validation = pure business rule di backend (bukan tabel baru): cek apakah `subject_score`, `attendance`, `health_record` sudah terisi untuk `record_id` tertentu → kembalikan daftar field yang belum lengkap.
- Buku Induk Preview murni **read** dan **format ulang** data yang sudah ada, tidak membuat entitas baru. Sistem **tidak** generate dokumen Buku Induk digital (ditulis manual oleh guru).

### 5.8 AI Features (FR-11, FR-12, FR-13)
Alur (14-ai-system-design.md):
```
Guru pilih siswa
   → Backend ambil data akademik (nilai, kehadiran, prestasi, kesehatan, catatan)
   → Backend susun prompt terstruktur
   → Kirim ke LLM API (OpenAI/Gemini)
   → Terima narasi
   → Simpan sebagai draft di DB_AI_SUMMARY (belum final)
   → Guru review → edit / terima / hapus / regenerate
   → Guru simpan versi final
```
- **Human-in-the-loop wajib**: hasil AI tidak pernah langsung dianggap final tanpa aksi guru.
- AI **tidak** mengubah data akademik sumber — hanya CREATE/UPDATE ke `DB_AI_SUMMARY`.
- 3 jenis `summary_type` (ENUM di skema): `student_summary`, `draft_description`, `transition_summary`.

### 5.9 Dashboard (FR-14)
- Agregasi sederhana (count query) — tidak perlu tabel/materialized view khusus di MVP.

---

## 6. Daftar Lengkap API Endpoint

### Auth
| Method | Endpoint | Role | Keterangan |
|---|---|---|---|
| POST | `/auth/login` | Semua | Return JWT access + refresh token |
| POST | `/auth/refresh` | Semua | Refresh access token |
| POST | `/auth/logout` | Semua | Invalidasi token/sesi |
| GET | `/auth/me` | Semua | Data user login + role |

### User Management
| Method | Endpoint | Role |
|---|---|---|
| GET | `/users` | Admin |
| POST | `/users` | Admin |
| GET | `/users/{user_id}` | Admin |
| PUT | `/users/{user_id}` | Admin |
| PATCH | `/users/{user_id}/status` | Admin |

### Academic Year
| Method | Endpoint | Role |
|---|---|---|
| GET | `/academic-years` | Admin |
| POST | `/academic-years` | Admin |
| PUT | `/academic-years/{id}` | Admin |
| PATCH | `/academic-years/{id}/activate` | Admin |
| PATCH | `/academic-years/{id}/archive` | Admin |

### Class & Teacher Assignment
| Method | Endpoint | Role |
|---|---|---|
| GET | `/classes` | Admin, Operator, Kepsek |
| POST | `/classes` | Admin |
| PUT | `/classes/{id}` | Admin |
| PATCH | `/classes/{id}/homeroom-teacher` | Admin |
| GET | `/classes/{id}/students` | Admin, Operator, Guru (kelasnya), Kepsek |

### Student
| Method | Endpoint | Role |
|---|---|---|
| GET | `/students` | Operator, Kepsek |
| POST | `/students` | Operator |
| GET | `/students/{id}` | Operator, Guru (jika kelasnya), Kepsek |
| PUT | `/students/{id}` | Operator |
| GET | `/students?class_id={id}` | Operator, Guru, Kepsek |

### Semester Record & Sub-resource
| Method | Endpoint | Role |
|---|---|---|
| POST | `/students/{id}/semester-records` | Guru |
| GET | `/semester-records/{id}` | Guru (kelasnya), Kepsek |
| PUT | `/semester-records/{id}` | Guru |
| POST | `/semester-records/{id}/subject-scores` | Guru |
| PUT | `/subject-scores/{score_id}` | Guru |
| DELETE | `/subject-scores/{score_id}` | Guru |
| PUT | `/semester-records/{id}/attendance` | Guru (upsert) |
| POST | `/semester-records/{id}/achievements` | Guru |
| PUT | `/achievements/{achievement_id}` | Guru |
| DELETE | `/achievements/{achievement_id}` | Guru |
| PUT | `/semester-records/{id}/health-record` | Guru (upsert) |

### Longitudinal Profile & Timeline
| Method | Endpoint | Role |
|---|---|---|
| GET | `/students/{id}/profile` | Guru (kelasnya), Kepsek |
| GET | `/students/{id}/timeline` | Guru (kelasnya), Kepsek |
| GET | `/students/{id}/semester-records/{semester_id}` | Guru (kelasnya), Kepsek |

### Administrative Workspace / Buku Induk
| Method | Endpoint | Role |
|---|---|---|
| GET | `/students/{id}/administrative-workspace` | Guru |
| GET | `/students/{id}/validation-status` | Guru |
| GET | `/students/{id}/buku-induk-preview` | Guru, Kepsek |

### AI
| Method | Endpoint | Role |
|---|---|---|
| POST | `/ai/students/{id}/summary` | Guru |
| POST | `/ai/students/{id}/draft-description` | Guru |
| POST | `/ai/classes/{id}/transition-summary` | Guru |
| GET | `/semester-records/{id}/ai-summaries` | Guru, Kepsek |
| PUT | `/ai-summaries/{id}` | Guru (revisi manual) |
| DELETE | `/ai-summaries/{id}` | Guru |

### Dashboard
| Method | Endpoint | Role |
|---|---|---|
| GET | `/dashboard/summary` | Semua (data disesuaikan role) |
| GET | `/dashboard/administrative-status` | Guru, Kepsek |

---

## 7. Yang SENGAJA Tidak Ada di MVP

Sesuai batasan eksplisit di dokumen (jangan dibangun dulu):
- Chatbot AI, RAG, Vector Database
- Prediksi prestasi siswa, Early Warning System
- Dashboard orang tua
- Notifikasi otomatis
- Integrasi Dapodik / sistem pemerintah
- Microservices, Docker, Kubernetes, Redis, Message Queue, API Gateway, CI/CD

Ini penting supaya scope development tidak melebar dari yang direncanakan tim untuk kompetisi LIDM.

---

## 8. Rekomendasi Tambahan Saat Implementasi

1. **Constraint unik** yang belum eksplisit di skema tapi sebaiknya ditambahkan: `(student_id, academic_year_id)` unique di `DB_SEMESTER_RECORD` agar tidak dobel record per siswa per tahun ajaran.
2. **Rate limiting** untuk endpoint AI (`/ai/*`) karena memanggil LLM API eksternal berbayar per-request.
3. **Audit log minimal** untuk perubahan `homeroom_teacher_id`, karena ini memengaruhi hak akses data siswa secara langsung.
4. **Versioning draft AI**: karena guru bisa "regenerate", pertimbangkan menyimpan histori draft (bukan overwrite) agar guru bisa bandingkan versi — meski skema saat ini hanya punya 1 baris per summary_type per record.