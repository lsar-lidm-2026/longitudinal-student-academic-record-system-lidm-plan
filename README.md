# Longitudinal Student Academic Record (LSAR)

**Pengembangan Sistem Longitudinal Student Academic Record Berbasis Artificial Intelligence untuk Mendukung Administrasi Akademik dan Penyusunan Buku Induk Siswa Sekolah Dasar**

Lomba Inovasi Digital Mahasiswa (LIDM) 2026 — Kategori: Inovasi Teknologi Digital Pendidikan

---

## Ringkasan

Sistem LSAR adalah aplikasi web yang membantu guru sekolah dasar dalam mengelola riwayat akademik siswa secara longitudinal (kelas 1 hingga kelas 6) dan mempersiapkan administrasi penyusunan Buku Induk Siswa.

Sistem dilengkapi dengan fitur Artificial Intelligence sebagai *decision support system* untuk membantu guru:
- Membuat ringkasan perkembangan siswa (AI Student Summary)
- Menyusun draft deskripsi rapor (AI Draft Deskripsi)
- Membantu transisi wali kelas baru (AI Student Transition Summary)

---

## Tim Pengembang

| Nama                        | Peran            |
|-----------------------------|------------------|
| Rio Andika Andriansyah      | Anggota Tim      |
| Asep Haryana Saputra        | Anggota Tim      |
| Dika Ramadani               | Anggota Tim      |
| Muhammad Abshar Hakim       | Anggota Tim      |

**Dosen Pembimbing:** Dede Husen, M.Kom.

---

## Tech Stack

| Komponen      | Teknologi                          |
|---------------|-------------------------------------|
| Frontend      | Next.js 16 + TypeScript + Tailwind CSS v4 |
| Backend       | Elysia (Bun web framework)         |
| ORM           | Prisma                             |
| Database      | PostgreSQL                    |
| Autentikasi   | JWT (manual)                       |
| AI            | OpenAI API / Google Gemini API     |
| Runtime       | Bun                                |

---

## Status Proyek

**Development** — Sedang dalam tahap implementasi berdasarkan dokumen perencanaan.

Milestone:
- [x] Planning & System Design
- [ ] Foundation (Sprint 1)
- [ ] Authentication (Sprint 2)
- [ ] Master Data (Sprint 3)
- [ ] Academic Record (Sprint 4)
- [ ] Longitudinal Profile (Sprint 5)
- [ ] AI Features (Sprint 6)
- [ ] Buku Induk (Sprint 7)
- [ ] Polish & Demo (Sprint 8)

Lihat [16-implementation-roadmap.md](./16-implementation-roadmap.md) untuk detail sprint.

---

## Struktur Repositori

```
├── apps/
│   ├── backend/          # Backend API Elysia + Prisma
│   └── frontend/         # Frontend Next.js 16
├── plan/                 # Dokumen perencanaan (submodule)
└── README.md
```

---

## Dokumentasi Perencanaan

| Dokumen                         | Deskripsi                                           |
|---------------------------------|-----------------------------------------------------|
| 00-project-overview.md          | Gambaran umum, latar belakang, tujuan               |
| 01-problem-analysis.md          | Analisis masalah dan root cause                     |
| 02-solution-overview.md         | Gambaran solusi dan nilai inovasi                   |
| 03-business-process.md          | Analisis proses bisnis As-Is dan To-Be              |
| 04-functional-requirement.md    | Kebutuhan fungsional dan non-fungsional             |
| 05-conceptual-erd.md            | Entity Relationship Diagram konseptual              |
| 06-logical-erd.md               | Entity Relationship Diagram logis                   |
| 07-physical-erd.md              | Entity Relationship Diagram fisik (database)        |
| 08-database-schema.md           | Skema database lengkap (Prisma)                     |
| 09-use-case-diagram.md          | Diagram use case per aktor                          |
| 10-activity-diagram.md          | Diagram aktivitas dengan swimlane                   |
| 11-sequence-diagram.md          | Diagram sekuensial detail per fitur                 |
| 12-ui-ux-design.md              | Desain antarmuka, wireframe, komponen               |
| 13-system-architecture.md       | Arsitektur sistem dan teknologi                     |
| 14-ai-system-design.md          | Desain AI, prompt engineering, error handling       |
| 15-desain-api-dan-flow.md       | API endpoint, business logic, authorization         |
| 16-implementation-roadmap.md    | Rencana implementasi per sprint                     |
| 17-project-structure.md         | Struktur folder dan organisasi kode                 |

---

## Cara Menjalankan

### Prasyarat

- Bun (https://bun.sh)
- PostgreSQL

### Backend

```bash
cd apps/backend
cp .env.example .env
# Edit .env dengan konfigurasi database
bun install
bun db:migrate
bun dev
```

### Frontend

```bash
cd apps/frontend
cp .env.example .env.local
# Edit .env.local dengan URL backend
bun install
bun dev
```

---

## Lisensi

Proyek ini dikembangkan untuk tujuan kompetisi LIDM 2026.
