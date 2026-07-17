# Physical Entity Relationship Diagram (Physical ERD)
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Physical ERD merupakan tahap implementasi dari Logical ERD ke dalam struktur database yang akan digunakan oleh sistem.

Dokumen ini telah diselaraskan dengan implementasi Prisma ORM dan database MySQL/MariaDB yang digunakan dalam pengembangan.

Karena sistem ini merupakan MVP untuk Lomba Inovasi Digital Mahasiswa (LIDM), desain database dibuat sesederhana mungkin tanpa mengurangi kebutuhan utama sistem.

---

# DB_USER

Deskripsi: Menyimpan akun seluruh pengguna sistem.  
Mapping: `user`

| Field     | Type         | Constraint          |
|-----------|--------------|---------------------|
| id        | UUID         | PK                  |
| username  | VARCHAR(100) | UNIQUE, NOT NULL    |
| password  | VARCHAR(255) | NOT NULL            |
| name      | VARCHAR(100) | NOT NULL            |
| role      | ENUM         | NOT NULL            |
| is_active | BOOLEAN      | DEFAULT TRUE        |
| created_at| TIMESTAMP    | DEFAULT NOW()       |
| updated_at| TIMESTAMP    | @updatedAt          |

ENUM Role: `ADMINISTRATOR`, `OPERATOR_SEKOLAH`, `GURU`, `KEPALA_SEKOLAH`

Index: 
- `idx_user_username` on `username`

---

# DB_ACADEMIC_YEAR

Deskripsi: Menyimpan informasi tahun ajaran.  
Mapping: `academic_year`

| Field       | Type         | Constraint          |
|-------------|--------------|---------------------|
| id          | UUID         | PK                  |
| year        | VARCHAR(20)  | UNIQUE, NOT NULL    |
| is_active   | BOOLEAN      | DEFAULT FALSE       |
| is_archived | BOOLEAN      | DEFAULT FALSE       |
| created_at  | TIMESTAMP    | DEFAULT NOW()       |
| updated_at  | TIMESTAMP    | @updatedAt          |

Index:
- `idx_academic_year_active` on `is_active` (filtered)
- `idx_academic_year_year` on `year`

---

# DB_CLASS

Deskripsi: Menyimpan data kelas dan wali kelas.  
Mapping: `class`

| Field              | Type         | Constraint              |
|--------------------|--------------|-------------------------|
| id                 | UUID         | PK                      |
| name               | VARCHAR(50)  | NOT NULL                |
| academic_year_id   | UUID         | FK → DB_ACADEMIC_YEAR   |
| homeroom_teacher_id| UUID         | FK → DB_USER (nullable) |
| created_at         | TIMESTAMP    | DEFAULT NOW()           |
| updated_at         | TIMESTAMP    | @updatedAt              |

FK:
- `academic_year_id` → DB_ACADEMIC_YEAR (Cascade)
- `homeroom_teacher_id` → DB_USER (Set Null)

Constraint: `@@unique([name, academic_year_id])`

Index:
- `idx_class_teacher` on `homeroom_teacher_id`
- `idx_class_academic_year` on `academic_year_id`

---

# DB_CLASS_AUDIT_LOG

Deskripsi: Log perubahan wali kelas (audit trail).  
Mapping: `class_audit_log`

| Field                | Type         | Constraint              |
|----------------------|--------------|-------------------------|
| id                   | UUID         | PK                      |
| class_id             | UUID         | FK → DB_CLASS           |
| previous_teacher_id  | UUID         | nullable                |
| new_teacher_id       | UUID         | nullable                |
| changed_by           | UUID         | FK → DB_USER            |
| changed_at           | TIMESTAMP    | DEFAULT NOW()           |

FK:
- `class_id` → DB_CLASS (Cascade)
- `changed_by` → DB_USER

Index:
- `idx_audit_log_class` on `class_id`
- `idx_audit_log_changed_at` on `changed_at`

---

# DB_STUDENT

Deskripsi: Menyimpan identitas siswa.  
Mapping: `student`

| Field     | Type         | Constraint              |
|-----------|--------------|-------------------------|
| id        | UUID         | PK                      |
| class_id  | UUID         | FK → DB_CLASS           |
| nis       | VARCHAR(20)  | UNIQUE, NOT NULL        |
| nisn      | VARCHAR(20)  | UNIQUE, NOT NULL        |
| name      | VARCHAR(100) | NOT NULL                |
| gender    | VARCHAR(10)  | NOT NULL                |
| created_at| TIMESTAMP    | DEFAULT NOW()           |
| updated_at| TIMESTAMP    | @updatedAt              |

FK:
- `class_id` → DB_CLASS

Index:
- `idx_student_nis` on `nis`
- `idx_student_nisn` on `nisn`
- `idx_student_class` on `class_id`
- `idx_student_name` on `name`

---

# DB_SEMESTER_RECORD

Deskripsi: Merupakan tabel utama sistem. Satu baris mewakili perkembangan seorang siswa pada satu semester.  
Mapping: `semester_record`

| Field            | Type         | Constraint              |
|------------------|--------------|-------------------------|
| id               | UUID         | PK                      |
| student_id       | UUID         | FK → DB_STUDENT         |
| academic_year_id | UUID         | FK → DB_ACADEMIC_YEAR   |
| semester         | INT          | NOT NULL (1/2)          |
| created_by       | UUID         | FK → DB_USER            |
| created_at       | TIMESTAMP    | DEFAULT NOW()           |
| updated_at       | TIMESTAMP    | @updatedAt              |

FK:
- `student_id` → DB_STUDENT (Cascade)
- `academic_year_id` → DB_ACADEMIC_YEAR
- `created_by` → DB_USER

Constraint: `@@unique([student_id, academic_year_id, semester])`

Index:
- `idx_semester_record_student` on `student_id`
- `idx_semester_record_academic_year` on `academic_year_id`
- `idx_semester_record_composite` on `(student_id, academic_year_id, semester)`

---

# DB_SUBJECT_SCORE

Deskripsi: Menyimpan nilai mata pelajaran per semester.  
Mapping: `subject_score`

| Field              | Type         | Constraint              |
|--------------------|--------------|-------------------------|
| id                 | UUID         | PK                      |
| semester_record_id | UUID         | FK → DB_SEMESTER_RECORD |
| subject_name       | VARCHAR(100) | NOT NULL                |
| knowledge_score    | FLOAT        | NOT NULL                |
| skills_score       | FLOAT        | NOT NULL                |
| notes              | TEXT         | nullable                |
| created_at         | TIMESTAMP    | DEFAULT NOW()           |
| updated_at         | TIMESTAMP    | @updatedAt              |

FK:
- `semester_record_id` → DB_SEMESTER_RECORD (Cascade)

Constraint: `@@unique([semester_record_id, subject_name])`

Index:
- `idx_subject_score_record` on `semester_record_id`
- `idx_subject_score_composite` on `(semester_record_id, subject_name)`

---

# DB_ATTENDANCE

Deskripsi: Menyimpan rekap kehadiran.  
Mapping: `attendance`

| Field              | Type         | Constraint              |
|--------------------|--------------|-------------------------|
| id                 | UUID         | PK                      |
| semester_record_id | UUID         | FK, UNIQUE              |
| sick               | INT          | DEFAULT 0               |
| permission         | INT          | DEFAULT 0               |
| absent             | INT          | DEFAULT 0               |
| created_at         | TIMESTAMP    | DEFAULT NOW()           |
| updated_at         | TIMESTAMP    | @updatedAt              |

FK:
- `semester_record_id` → DB_SEMESTER_RECORD (Cascade)

Constraint UNIQUE pada `semester_record_id` menjamin relasi 1:1.

---

# DB_ACHIEVEMENT

Deskripsi: Menyimpan prestasi siswa.  
Mapping: `achievement`

| Field              | Type         | Constraint              |
|--------------------|--------------|-------------------------|
| id                 | UUID         | PK                      |
| semester_record_id | UUID         | FK → DB_SEMESTER_RECORD |
| title              | VARCHAR(150) | NOT NULL                |
| type               | VARCHAR(50)  | NOT NULL                |
| description        | TEXT         | nullable                |
| created_at         | TIMESTAMP    | DEFAULT NOW()           |
| updated_at         | TIMESTAMP    | @updatedAt              |

FK:
- `semester_record_id` → DB_SEMESTER_RECORD (Cascade)

Index:
- `idx_achievement_record` on `semester_record_id`

---

# DB_HEALTH_RECORD

Deskripsi: Menyimpan data kesehatan siswa.  
Mapping: `health_record`

| Field              | Type         | Constraint              |
|--------------------|--------------|-------------------------|
| id                 | UUID         | PK                      |
| semester_record_id | UUID         | FK, UNIQUE              |
| height             | FLOAT        | nullable                |
| weight             | FLOAT        | nullable                |
| hearing_condition  | VARCHAR(50)  | nullable                |
| vision_condition   | VARCHAR(50)  | nullable                |
| teeth_condition    | VARCHAR(50)  | nullable                |
| created_at         | TIMESTAMP    | DEFAULT NOW()           |
| updated_at         | TIMESTAMP    | @updatedAt              |

FK:
- `semester_record_id` → DB_SEMESTER_RECORD (Cascade)

Constraint UNIQUE pada `semester_record_id` menjamin relasi 1:1.

---

# DB_AI_SUMMARY

Deskripsi: Menyimpan hasil keluaran Artificial Intelligence.  
Mapping: `ai_summary`

| Field              | Type         | Constraint              |
|--------------------|--------------|-------------------------|
| id                 | UUID         | PK                      |
| semester_record_id | UUID         | FK → DB_SEMESTER_RECORD |
| summary_type       | ENUM         | NOT NULL                |
| content            | TEXT         | NOT NULL                |
| is_final           | BOOLEAN      | DEFAULT FALSE           |
| version            | INT          | DEFAULT 1               |
| created_at         | TIMESTAMP    | DEFAULT NOW()           |
| updated_at         | TIMESTAMP    | @updatedAt              |

ENUM SummaryType: `STUDENT_SUMMARY`, `DRAFT_DESCRIPTION`, `TRANSITION_SUMMARY`

FK:
- `semester_record_id` → DB_SEMESTER_RECORD (Cascade)

Constraint: `@@unique([semester_record_id, summary_type, version])`

Index:
- `idx_ai_summary_record` on `semester_record_id`
- `idx_ai_summary_type` on `summary_type`

---

# Relasi Antar Tabel

| Parent Table       | Child Table         | FK Column             | Relationship |
|--------------------|---------------------|-----------------------|--------------|
| DB_USER            | DB_CLASS            | homeroom_teacher_id   | 1 : N        |
| DB_USER            | DB_SEMESTER_RECORD  | created_by            | 1 : N        |
| DB_USER            | DB_CLASS_AUDIT_LOG  | changed_by            | 1 : N        |
| DB_ACADEMIC_YEAR   | DB_CLASS            | academic_year_id      | 1 : N        |
| DB_ACADEMIC_YEAR   | DB_SEMESTER_RECORD  | academic_year_id      | 1 : N        |
| DB_CLASS           | DB_STUDENT          | class_id              | 1 : N        |
| DB_CLASS           | DB_CLASS_AUDIT_LOG  | class_id              | 1 : N        |
| DB_STUDENT         | DB_SEMESTER_RECORD  | student_id            | 1 : N        |
| DB_SEMESTER_RECORD | DB_SUBJECT_SCORE    | semester_record_id    | 1 : N        |
| DB_SEMESTER_RECORD | DB_ATTENDANCE       | semester_record_id    | 1 : 1        |
| DB_SEMESTER_RECORD | DB_ACHIEVEMENT      | semester_record_id    | 1 : N        |
| DB_SEMESTER_RECORD | DB_HEALTH_RECORD    | semester_record_id    | 1 : 1        |
| DB_SEMESTER_RECORD | DB_AI_SUMMARY       | semester_record_id    | 1 : N        |

---

# Normalisasi

Desain database telah memenuhi prinsip normalisasi dasar.

**First Normal Form (1NF):**
- Seluruh atribut bersifat atomik.
- Tidak terdapat data berulang dalam satu kolom.

**Second Normal Form (2NF):**
- Seluruh atribut bergantung sepenuhnya pada Primary Key.

**Third Normal Form (3NF):**
- Tidak terdapat ketergantungan transitif.
- Informasi dipisahkan ke tabel yang sesuai.

---

# Ringkasan Index

| Tabel              | Index                            | Tipe      |
|--------------------|----------------------------------|-----------|
| user               | `idx_user_username`              | UNIQUE    |
| academic_year      | `idx_academic_year_active`       | INDEX     |
| academic_year      | `idx_academic_year_year`         | UNIQUE    |
| class              | `idx_class_teacher`              | INDEX     |
| class              | `idx_class_academic_year`        | INDEX     |
| class              | `uq_class_name_year`             | UNIQUE    |
| class_audit_log    | `idx_audit_log_class`            | INDEX     |
| class_audit_log    | `idx_audit_log_changed_at`       | INDEX     |
| student            | `idx_student_nis`                | UNIQUE    |
| student            | `idx_student_nisn`               | UNIQUE    |
| student            | `idx_student_class`              | INDEX     |
| subject_score      | `idx_subject_score_record`       | INDEX     |
| subject_score      | `uq_subject_record_name`         | UNIQUE    |
| semester_record    | `idx_semester_composite`         | UNIQUE    |
| ai_summary         | `idx_ai_summary_record`          | INDEX     |
| ai_summary         | `uq_summary_type_version`        | UNIQUE    |

---

# Catatan Implementasi MVP

Untuk menjaga ruang lingkup tetap sederhana dan sesuai dengan kompetisi, beberapa keputusan desain diambil:

1. Tidak membuat tabel Subject karena daftar mata pelajaran sedikit dan relatif tetap.
2. Tidak membuat tabel Role maupun Permission terpisah — menggunakan ENUM.
3. Tidak membuat tabel School karena sistem hanya mendukung satu sekolah sebagai studi kasus.
4. Tidak membuat tabel Notification, Audit Log umum, maupun Session.
5. Nilai dipisah menjadi `knowledge_score` dan `skills_score` sesuai Kurikulum Merdeka.
6. Data kesehatan diperluas dengan kondisi pendengaran, penglihatan, dan gigi.
7. ClassAuditLog ditambahkan untuk audit trail perubahan wali kelas.
8. AiSummary memiliki `is_final` dan `version` untuk mendukung human-in-the-loop dan versioning draft.
9. Tidak menyimpan embedding, prompt, maupun metadata AI — fitur RAG dan Vector Database merupakan rencana pengembangan lanjutan.

Dengan struktur ini, database sudah mampu mendukung seluruh fitur MVP: pengelolaan riwayat akademik longitudinal, penyusunan administrasi Buku Induk, serta fitur AI Student Summary, AI Draft Deskripsi, dan AI Student Transition Summary.
