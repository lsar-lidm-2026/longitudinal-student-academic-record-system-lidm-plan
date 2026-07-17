# Project Structure
## Longitudinal Student Academic Record (LSAR) — LIDM 2026

# Pendahuluan

Dokumen ini menjelaskan struktur folder dan organisasi kode untuk pengembangan sistem LSAR. Struktur ini dirancang untuk memisahkan concern (separation of concerns) antara backend, frontend, dan dokumentasi perencanaan.

```
longitudinal-student-academic-record-system-lidm/
├── apps/
│   ├── backend/          # Backend API (Elysia + Bun)
│   └── frontend/         # Frontend (Next.js 16)
├── plan/                 # Submodule: dokumentasi perencanaan
│   ├── 00-project-overview.md
│   ├── 01-problem-analysis.md
│   ├── 02-solution-overview.md
│   ├── 03-business-process.md
│   ├── 04-functional-requirement.md
│   ├── 05-conceptual-erd.md
│   ├── 06-logical-erd.md
│   ├── 07-physical-erd.md
│   ├── 08-database-schema.md
│   ├── 09-use-case-diagram.md
│   ├── 10-activity-diagram.md
│   ├── 11-sequence-diagram.md
│   ├── 12-ui-ux-design.md
│   ├── 13-system-architecture.md
│   ├── 14-ai-system-design.md
│   ├── 15-desain-api-dan-flow.md
│   ├── 16-implementation-roadmap.md
│   └── 17-project-structure.md
├── .gitignore
├── .gitmodules
└── README.md
```

---

# Backend Structure (`apps/backend/`)

```
apps/backend/
├── prisma/
│   ├── schema.prisma           # Definisi database (models, enums, relations)
│   └── migrations/              # Riwayat migrasi database (auto-generated)
│
├── src/
│   ├── index.ts                 # Entry point: inisialisasi Elysia app
│   ├── config/
│   │   ├── env.ts              # Environment variables & konfigurasi
│   │   └── database.ts         # Inisialisasi Prisma client
│   │
│   ├── middleware/
│   │   ├── auth.ts             # requireAuth: verifikasi JWT
│   │   ├── role.ts             # requireRole: cek role user
│   │   └── homeroom.ts         # requireHomeroomAccess: cek teacher assignment
│   │
│   ├── modules/
│   │   ├── auth/
│   │   │   ├── auth.controller.ts   # Handler login, refresh, logout
│   │   │   ├── auth.service.ts      # Logika bisnis auth
│   │   │   └── auth.validation.ts   # Validasi input (zod/elysia)
│   │   │
│   │   ├── users/
│   │   │   ├── user.controller.ts
│   │   │   ├── user.service.ts
│   │   │   └── user.validation.ts
│   │   │
│   │   ├── academic-years/
│   │   │   ├── academic-year.controller.ts
│   │   │   ├── academic-year.service.ts
│   │   │   └── academic-year.validation.ts
│   │   │
│   │   ├── classes/
│   │   │   ├── class.controller.ts
│   │   │   ├── class.service.ts
│   │   │   └── class.validation.ts
│   │   │
│   │   ├── students/
│   │   │   ├── student.controller.ts
│   │   │   ├── student.service.ts
│   │   │   └── student.validation.ts
│   │   │
│   │   ├── semester-records/
│   │   │   ├── semester-record.controller.ts
│   │   │   ├── semester-record.service.ts
│   │   │   └── semester-record.validation.ts
│   │   │
│   │   ├── subject-scores/
│   │   │   ├── subject-score.controller.ts
│   │   │   ├── subject-score.service.ts
│   │   │   └── subject-score.validation.ts
│   │   │
│   │   ├── attendance/
│   │   │   ├── attendance.controller.ts
│   │   │   ├── attendance.service.ts
│   │   │   └── attendance.validation.ts
│   │   │
│   │   ├── achievements/
│   │   │   ├── achievement.controller.ts
│   │   │   ├── achievement.service.ts
│   │   │   └── achievement.validation.ts
│   │   │
│   │   ├── health-records/
│   │   │   ├── health-record.controller.ts
│   │   │   ├── health-record.service.ts
│   │   │   └── health-record.validation.ts
│   │   │
│   │   ├── ai/
│   │   │   ├── ai.controller.ts           # Handler 3 fitur AI
│   │   │   ├── ai.service.ts              # Logika AI prompt & LLM call
│   │   │   ├── ai.validation.ts
│   │   │   ├── prompts.ts                 # Template prompt untuk tiap fitur
│   │   │   └── llm.client.ts             # HTTP client untuk LLM API
│   │   │
│   │   ├── buku-induk/
│   │   │   ├── buku-induk.controller.ts   # Preview, workspace, validation
│   │   │   ├── buku-induk.service.ts
│   │   │   └── buku-induk.validation.ts
│   │   │
│   │   └── dashboard/
│   │       ├── dashboard.controller.ts
│   │       └── dashboard.service.ts
│   │
│   ├── common/
│   │   ├── response.ts          # Format response seragam
│   │   ├── error.ts             # Custom error classes
│   │   ├── pagination.ts        # Helper pagination
│   │   └── types.ts             # Shared types & interfaces
│   │
│   └── lib/
│       ├── jwt.ts               # Helper JWT sign & verify
│       ├── hash.ts              # Helper hashing password
│       └── prisma.ts            # Prisma client singleton
│
├── generated/
│   └── prisma/                  # Prisma client (auto-generated)
│
├── .env.example                 # Template environment variables
├── package.json
├── tsconfig.json
└── README.md
```

### Backend Convention

| Pattern           | Aturan                                                    |
|-------------------|-----------------------------------------------------------|
| File naming       | kebab-case (`academic-year.controller.ts`)                |
| Class naming      | PascalCase (`AcademicYearService`)                        |
| Function naming   | camelCase (`createAcademicYear`)                          |
| Module structure  | controller → service → prisma (3-layer)                   |
| Validation        | Validasi input menggunakan Elysia built-in atau zod       |
| Error handling    | throw custom error → global error handler → response      |
| Response format   | Seragam: `{ success, data, meta?, error? }`               |
| ORM               | Prisma (type-safe query, auto-completion)                 |

### Backend Layer Architecture

```
Controller (HTTP Layer)
    ↓ Menerima request, memanggil service, mengembalikan response
Service (Business Logic Layer)
    ↓ Validasi bisnis, orchestrasi data, panggil Prisma
Prisma (Data Access Layer)
    ↓ Query ke database MySQL/MariaDB
```

---

# Frontend Structure (`apps/frontend/`)

```
apps/frontend/
├── app/
│   ├── layout.tsx              # Root layout (font, metadata, global styles)
│   ├── page.tsx                # Halaman utama (redirect ke dashboard/login)
│   ├── globals.css             # Global styles & Tailwind imports
│   ├── favicon.ico
│   │
│   ├── (auth)/
│   │   └── login/
│   │       └── page.tsx        # Halaman login
│   │
│   ├── (dashboard)/
│   │   ├── layout.tsx          # Layout sidebar + navbar
│   │   ├── page.tsx            # Dashboard utama
│   │   │
│   │   ├── students/
│   │   │   ├── page.tsx        # Daftar siswa
│   │   │   └── [id]/
│   │   │       ├── page.tsx    # Detail siswa + timeline
│   │   │       ├── semester-records/
│   │   │       │   └── page.tsx # Input semester
│   │   │       ├── ai/
│   │   │       │   └── page.tsx # AI Assistant
│   │   │       └── buku-induk/
│   │   │           └── page.tsx # Preview Buku Induk
│   │   │
│   │   ├── classes/
│   │   │   └── page.tsx        # Manajemen kelas (admin)
│   │   │
│   │   ├── academic-years/
│   │   │   └── page.tsx        # Manajemen tahun ajaran (admin)
│   │   │
│   │   ├── users/
│   │   │   └── page.tsx        # Manajemen pengguna (admin)
│   │   │
│   │   └── settings/
│   │       └── page.tsx        # Pengaturan profil
│   │
│   └── api/                    # (Opsional) API routes Next.js jika perlu
│
├── components/
│   ├── ui/                     # Komponen UI reusable
│   │   ├── Button.tsx
│   │   ├── Card.tsx
│   │   ├── Modal.tsx
│   │   ├── Table.tsx
│   │   ├── Badge.tsx
│   │   ├── Input.tsx
│   │   ├── Select.tsx
│   │   └── ProgressBar.tsx
│   │
│   ├── layout/
│   │   ├── Sidebar.tsx         # Sidebar navigasi
│   │   ├── Navbar.tsx          # Top navbar
│   │   └── AuthGuard.tsx       # Protected route wrapper
│   │
│   ├── students/
│   │   ├── StudentList.tsx     # Tabel daftar siswa
│   │   ├── StudentCard.tsx     # Card biodata siswa
│   │   ├── Timeline.tsx        # Komponen timeline longitudinal
│   │   └── SemesterDetail.tsx  # Detail satu semester
│   │
│   ├── academic/
│   │   ├── ScoreInput.tsx      # Form input nilai (knowledge + skills)
│   │   ├── AttendanceInput.tsx # Form input kehadiran
│   │   ├── AchievementList.tsx # Daftar & form prestasi
│   │   └── HealthInput.tsx     # Form input kesehatan
│   │
│   ├── ai/
│   │   ├── AiAssistant.tsx     # Container AI Assistant
│   │   ├── SummaryResult.tsx   # Menampilkan hasil AI
│   │   ├── DraftEditor.tsx     # Editor untuk review/edit draft
│   │   └── VersionCompare.tsx  # Perbandingan versi draft
│   │
│   └── buku-induk/
│       ├── BukuIndukPreview.tsx # Preview Buku Induk
│       └── ValidationStatus.tsx # Status kelengkapan data
│
├── lib/
│   ├── api.ts                  # HTTP client (fetch wrapper)
│   ├── auth.ts                 # Auth context & hooks
│   └── utils.ts                # Utility functions
│
├── hooks/
│   ├── useAuth.ts              # Hook autentikasi
│   ├── useStudents.ts          # Hook data siswa
│   └── useSemesterRecords.ts   # Hook data semester
│
├── types/
│   └── index.ts                # TypeScript interfaces & types
│
├── public/
│   ├── next.svg
│   └── vercel.svg
│
├── .env.example                # Template env untuk frontend
├── next.config.ts
├── package.json
├── tsconfig.json
├── eslint.config.mjs
└── postcss.config.mjs
```

### Frontend Convention

| Pattern        | Aturan                                                  |
|----------------|---------------------------------------------------------|
| Framework      | Next.js 16 (App Router)                                 |
| Styling        | Tailwind CSS v4 (utility-first, no CSS modules)         |
| Component      | Function components dengan TypeScript                   |
| State          | Local state + custom hooks (tanpa Redux/Zustand di MVP) |
| API calls      | Custom `fetch` wrapper di `lib/api.ts`                  |
| Routing        | Next.js App Router (file-based routing)                 |
| Forms          | Controlled components dengan state lokal                |

---

# Environment Variables

## Backend (`apps/backend/.env`)

```env
# Database
DATABASE_URL="mysql://user:password@localhost:3306/lsar_db"

# JWT
JWT_SECRET="your-secret-key-min-32-chars"
JWT_EXPIRES_IN="7d"

# LLM API (OpenAI)
OPENAI_API_KEY="sk-..."
OPENAI_MODEL="gpt-4o-mini"

# Atau Google Gemini
# GEMINI_API_KEY="..."
# GEMINI_MODEL="gemini-1.5-flash"

# Server
PORT=3001
```

## Frontend (`apps/frontend/.env.local`)

```env
NEXT_PUBLIC_API_URL="http://localhost:3001/api"
```

---

# Git Workflow

## Branch Strategy

```
main              ← Production-ready code
  └── develop     ← Integration branch
       ├── feat/backend/auth
       ├── feat/frontend/login
       ├── feat/backend/academic-year
       └── fix/xxx
```

## Commit Convention

Format: `type(scope): description`

| Type     | Kegunaan                          |
|----------|-----------------------------------|
| feat     | Fitur baru                        |
| fix      | Perbaikan bug                     |
| docs     | Perubahan dokumentasi             |
| refactor | Refactoring kode                  |
| style    | Perubahan format/styles           |
| chore    | Tugas maintenance                 |
| db       | Perubahan schema database         |

Contoh:
```
feat(backend): add login endpoint with JWT
feat(frontend): create student timeline component
fix(backend): handle duplicate semester record
db(migration): add unique constraint to subject_score
```

## Code Review Checklist

Sebelum merge, pastikan:
- [ ] Kode sudah di-test secara manual
- [ ] Tidak ada error TypeScript (`bun run check` / `tsc --noEmit`)
- [ ] Mengikuti convention yang sudah ditetapkan
- [ ] Tidak ada hardcoded value (gunakan env variable)
- [ ] Error handling sudah sesuai
- [ ] Response format seragam
- [ ] Tidak ada kode yang di-comment tanpa alasan
