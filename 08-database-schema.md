# Database Schema
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Database Schema merupakan representasi struktur database yang akan digunakan dalam implementasi sistem.

Skema ini ditulis dalam format Prisma Schema Language dan diimplementasikan menggunakan Prisma ORM dengan database MySQL/MariaDB.

Karena sistem ini merupakan Minimum Viable Product (MVP) untuk Lomba Inovasi Digital Mahasiswa (LIDM), skema database hanya mencakup kebutuhan inti sistem tanpa menambahkan komponen yang belum diperlukan.

---

# Diagram Relasi

```
User ──┬──→ Class (homeroomTeacherId)           [1:N - Teacher Assignment]
       └──→ SemesterRecord (createdById)        [1:N - Record Creator]
       └──→ ClassAuditLog (changedById)          [1:N - Audit Trail]

AcademicYear ──┬──→ Class                        [1:N]
               └──→ SemesterRecord               [1:N]

Class ──┬──→ Student                             [1:N]
        └──→ ClassAuditLog                       [1:N - Audit Trail]

Student ──→ SemesterRecord                       [1:N - Longitudinal Record]

SemesterRecord ──┬──→ SubjectScore               [1:N - Nilai per Mapel]
                 ├──→ Attendance                  [1:1 - Kehadiran]
                 ├──→ Achievement                 [1:N - Prestasi]
                 ├──→ HealthRecord                [1:1 - Kesehatan]
                 └──→ AiSummary                   [1:N - Hasil AI]
```

---

# Model: User

Menyimpan informasi seluruh pengguna sistem.

| Field     | Type     | Constraint          |
|-----------|----------|---------------------|
| id        | String   | PK, UUID            |
| username  | String   | UNIQUE, NOT NULL    |
| password  | String   | NOT NULL (hashed)   |
| name      | String   | NOT NULL            |
| role      | Enum     | NOT NULL            |
| isActive  | Boolean  | DEFAULT true        |

Enum Role: `ADMINISTRATOR`, `OPERATOR_SEKOLAH`, `GURU`, `KEPALA_SEKOLAH`

Relasi:
- `managedClasses` → Class (homeroomTeacherId) — Teacher Assignment
- `createdRecords` → SemesterRecord (createdById) — Pembuat record
- `auditLogs` → ClassAuditLog (changedById) — Audit trail

Mapping tabel: `user`

---

# Model: AcademicYear

Menyimpan informasi tahun ajaran yang digunakan sistem.

| Field      | Type    | Constraint          |
|------------|---------|---------------------|
| id         | String  | PK, UUID            |
| year       | String  | UNIQUE, NOT NULL    |
| isActive   | Boolean | DEFAULT false       |
| isArchived | Boolean | DEFAULT false       |

Catatan:
- Hanya 1 AcademicYear yang boleh memiliki `isActive = true` dalam satu waktu.
- Semester tidak disimpan di tabel ini, melainkan di SemesterRecord sebagai field `semester` (Int: 1 = Ganjil, 2 = Genap).

Relasi:
- `classes` → Class
- `semesterRecords` → SemesterRecord

Mapping tabel: `academic_year`

---

# Model: Class

Menyimpan informasi kelas dan wali kelas.

| Field             | Type   | Constraint              |
|-------------------|--------|-------------------------|
| id                | String | PK, UUID                |
| name              | String | NOT NULL                |
| academicYearId    | String | FK → AcademicYear       |
| homeroomTeacherId | String | FK → User (nullable)    |

Constraint:
- `@@unique([name, academicYearId])` — Nama kelas unik dalam satu tahun ajaran

Relasi:
- `academicYear` → AcademicYear
- `homeroomTeacher` → User
- `students` → Student
- `classAuditLogs` → ClassAuditLog

Mapping tabel: `class`

---

# Model: ClassAuditLog

Menyimpan log perubahan wali kelas (audit trail).

| Field               | Type     | Constraint          |
|---------------------|----------|---------------------|
| id                  | String   | PK, UUID            |
| classId             | String   | FK → Class          |
| previousTeacherId   | String?  | (nullable)          |
| newTeacherId        | String?  | (nullable)          |
| changedById         | String   | FK → User           |
| changedAt           | DateTime | DEFAULT now()       |

Relasi:
- `class` → Class (Cascade on delete)
- `changedBy` → User

Mapping tabel: `class_audit_log`

---

# Model: Student

Menyimpan identitas dasar siswa.

| Field    | Type   | Constraint          |
|----------|--------|---------------------|
| id       | String | PK, UUID            |
| nis      | String | UNIQUE, NOT NULL    |
| nisn     | String | UNIQUE, NOT NULL    |
| name     | String | NOT NULL            |
| gender   | String | NOT NULL            |
| classId  | String | FK → Class          |

Catatan:
- NIS dan NISN bersifat UNIQUE untuk validasi data
- Alamat, tanggal lahir, nama orang tua tidak disimpan di MVP (bisa ditambahkan jika diperlukan)

Relasi:
- `class` → Class
- `semesterRecords` → SemesterRecord

Mapping tabel: `student`

---

# Model: SemesterRecord

Merupakan model utama sistem Longitudinal Student Academic Record.
Satu baris mewakili perkembangan seorang siswa pada satu semester.

| Field          | Type     | Constraint              |
|----------------|----------|-------------------------|
| id             | String   | PK, UUID                |
| studentId      | String   | FK → Student            |
| academicYearId | String   | FK → AcademicYear       |
| semester       | Int      | NOT NULL (1=Ganjil, 2=Genap) |
| createdById    | String   | FK → User               |

Constraint:
- `@@unique([studentId, academicYearId, semester])` — Mencegah duplikasi record per siswa per tahun ajaran per semester

Relasi:
- `student` → Student (Cascade on delete)
- `academicYear` → AcademicYear
- `creator` → User ("RecordCreator")
- `subjectScores` → SubjectScore[]
- `attendance` → Attendance? (1:1 opsional)
- `achievements` → Achievement[]
- `healthRecord` → HealthRecord? (1:1 opsional)
- `aiSummaries` → AiSummary[]

Mapping tabel: `semester_record`

---

# Model: SubjectScore

Menyimpan nilai mata pelajaran setiap semester.

| Field            | Type   | Constraint                  |
|------------------|--------|-----------------------------|
| id               | String | PK, UUID                    |
| semesterRecordId | String | FK → SemesterRecord         |
| subjectName      | String | NOT NULL                    |
| knowledgeScore   | Float  | NOT NULL (Nilai Pengetahuan)|
| skillsScore      | Float  | NOT NULL (Nilai Keterampilan)|
| notes            | String?| Text, optional              |

Constraint:
- `@@unique([semesterRecordId, subjectName])` — Satu mapel hanya satu baris per semester

Relasi:
- `semesterRecord` → SemesterRecord (Cascade on delete)

Mapping tabel: `subject_score`

---

# Model: Attendance

Menyimpan rekap kehadiran siswa.

| Field            | Type   | Constraint                  |
|------------------|--------|-----------------------------|
| id               | String | PK, UUID                    |
| semesterRecordId | String | FK, UNIQUE → SemesterRecord |
| sick             | Int    | DEFAULT 0 (Sakit)           |
| permission       | Int    | DEFAULT 0 (Izin)            |
| absent           | Int    | DEFAULT 0 (Tanpa Keterangan)|

Relasi:
- `semesterRecord` → SemesterRecord (Cascade on delete)

Constraint UNIQUE pada `semesterRecordId` menjamin relasi 1:1. Gunakan operasi upsert.

Mapping tabel: `attendance`

---

# Model: Achievement

Menyimpan data prestasi siswa.

| Field            | Type   | Constraint          |
|------------------|--------|---------------------|
| id               | String | PK, UUID            |
| semesterRecordId | String | FK → SemesterRecord |
| title            | String | NOT NULL            |
| type             | String | NOT NULL            |
| description      | String?| Text, optional      |

`type`: `"Akademik"` atau `"Non-Akademik"`

Relasi:
- `semesterRecord` → SemesterRecord (Cascade on delete)

Mapping tabel: `achievement`

---

# Model: HealthRecord

Menyimpan data kesehatan siswa.

| Field            | Type   | Constraint                  |
|------------------|--------|-----------------------------|
| id               | String | PK, UUID                    |
| semesterRecordId | String | FK, UNIQUE → SemesterRecord |
| height           | Float? | Tinggi Badan (cm)           |
| weight           | Float? | Berat Badan (kg)            |
| hearingCondition | String?| Kondisi Pendengaran         |
| visionCondition  | String?| Kondisi Penglihatan         |
| teethCondition   | String?| Kondisi Gigi                |

Constraint UNIQUE pada `semesterRecordId` menjamin relasi 1:1. Gunakan operasi upsert.

Relasi:
- `semesterRecord` → SemesterRecord (Cascade on delete)

Mapping tabel: `health_record`

---

# Model: AiSummary

Menyimpan hasil keluaran Artificial Intelligence.

| Field            | Type      | Constraint                    |
|------------------|-----------|-------------------------------|
| id               | String    | PK, UUID                      |
| semesterRecordId | String    | FK → SemesterRecord           |
| summaryType      | Enum      | NOT NULL                      |
| content          | String    | NOT NULL (Text)               |
| isFinal          | Boolean   | DEFAULT false                 |
| version          | Int       | DEFAULT 1                     |

Enum SummaryType: `STUDENT_SUMMARY`, `DRAFT_DESCRIPTION`, `TRANSITION_SUMMARY`

Constraint:
- `@@unique([semesterRecordId, summaryType, version])` — Multi-versi draft

Catatan:
- `isFinal = false` = draft (belum direview guru)
- `isFinal = true` = sudah divalidasi guru
- `version` memungkinkan guru membandingkan hasil regenerate

Relasi:
- `semesterRecord` → SemesterRecord (Cascade on delete)

Mapping tabel: `ai_summary`

---

# Ringkasan Tabel

| Tabel           | Nama Prisma       | Fungsi                                       |
|-----------------|-------------------|----------------------------------------------|
| user            | User              | Menyimpan akun pengguna sistem               |
| academic_year   | AcademicYear      | Menyimpan tahun ajaran                       |
| class           | Class             | Menyimpan data kelas dan wali kelas          |
| class_audit_log | ClassAuditLog     | Log perubahan wali kelas                     |
| student         | Student           | Menyimpan biodata siswa                      |
| semester_record | SemesterRecord    | Menyimpan riwayat akademik setiap semester   |
| subject_score   | SubjectScore      | Menyimpan nilai setiap mata pelajaran        |
| attendance      | Attendance        | Menyimpan rekap kehadiran                    |
| achievement     | Achievement       | Menyimpan prestasi siswa                     |
| health_record   | HealthRecord      | Menyimpan data kesehatan siswa               |
| ai_summary      | AiSummary         | Menyimpan hasil ringkasan AI                 |

---

# Prinsip Desain Database

1. Satu siswa memiliki satu identitas utama (NIS/NISN) yang digunakan selama masa belajar.
2. Riwayat akademik disimpan per semester sehingga perkembangan siswa dapat ditelusuri secara longitudinal.
3. Artificial Intelligence tidak menyimpan data akademik baru, melainkan menghasilkan ringkasan berdasarkan data yang telah tersedia (read-only terhadap data sumber).
4. Database hanya menyimpan data yang benar-benar diperlukan untuk mendukung fitur MVP.
5. Struktur dibuat sederhana agar mudah dikembangkan, dipahami tim, dan didemonstrasikan dalam kompetisi.
6. Seluruh constraint unique dan foreign key diimplementasikan di level database untuk menjaga integritas data.
7. Operasi upsert digunakan untuk tabel dengan relasi 1:1 (Attendance, HealthRecord).

---

# Ruang Lingkup MVP

Database ini hanya mendukung fitur-fitur utama berikut:

- Login pengguna
- Manajemen kelas
- Manajemen siswa
- Riwayat akademik longitudinal
- Nilai mata pelajaran (pengetahuan + keterampilan)
- Rekap kehadiran
- Rekap prestasi
- Rekap kesehatan
- AI Student Summary
- AI Draft Deskripsi
- AI Student Transition Summary
- Administrative Preparation Workspace
- Preview Buku Induk

Pengembangan lanjutan yang belum termasuk dalam skema database ini meliputi:

- Chatbot AI
- RAG (Retrieval-Augmented Generation)
- Vector Database
- Prediksi Prestasi Siswa
- Early Warning System
- Dashboard Orang Tua
- Notifikasi Otomatis
