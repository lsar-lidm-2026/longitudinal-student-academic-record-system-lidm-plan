# Database Schema
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Database Schema merupakan representasi struktur database yang akan digunakan dalam implementasi sistem.

Berbeda dengan Physical ERD yang berfokus pada hubungan antar tabel, Database Schema menjelaskan struktur setiap tabel secara lebih sistematis sehingga mudah diimplementasikan menggunakan Relational Database Management System (RDBMS) seperti PostgreSQL atau MySQL.

Karena sistem ini merupakan Minimum Viable Product (MVP) untuk Lomba Inovasi Digital Mahasiswa (LIDM), skema database hanya mencakup kebutuhan inti sistem tanpa menambahkan komponen yang belum diperlukan.

---

# DB_USER

Deskripsi

Menyimpan informasi seluruh pengguna sistem.

Primary Key

user_id

| Column | Type | Constraint |
|---------|------|------------|
| user_id | UUID | PRIMARY KEY |
| full_name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(100) | UNIQUE, NOT NULL |
| password_hash | VARCHAR(255) | NOT NULL |
| role | ENUM | NOT NULL |
| is_active | BOOLEAN | DEFAULT TRUE |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

---

# DB_ACADEMIC_YEAR

Deskripsi

Menyimpan informasi tahun ajaran yang digunakan sistem.

Primary Key

academic_year_id

| Column | Type | Constraint |
|---------|------|------------|
| academic_year_id | UUID | PRIMARY KEY |
| academic_year | VARCHAR(20) | NOT NULL |
| semester | ENUM | NOT NULL |
| is_active | BOOLEAN | DEFAULT FALSE |

---

# DB_CLASS

Deskripsi

Menyimpan informasi kelas dan wali kelas.

Primary Key

class_id

Foreign Key

academic_year_id

homeroom_teacher_id

| Column | Type | Constraint |
|---------|------|------------|
| class_id | UUID | PRIMARY KEY |
| academic_year_id | UUID | FOREIGN KEY |
| homeroom_teacher_id | UUID | FOREIGN KEY |
| class_name | VARCHAR(20) | NOT NULL |
| grade | INTEGER | NOT NULL |

---

# DB_STUDENT

Deskripsi

Menyimpan identitas dasar siswa.

Primary Key

student_id

Foreign Key

class_id

| Column | Type | Constraint |
|---------|------|------------|
| student_id | UUID | PRIMARY KEY |
| class_id | UUID | FOREIGN KEY |
| nis | VARCHAR(20) | |
| nisn | VARCHAR(20) | |
| full_name | VARCHAR(100) | NOT NULL |
| gender | ENUM | |
| birth_date | DATE | |
| address | TEXT | |
| parent_name | VARCHAR(100) | |

---

# DB_SEMESTER_RECORD

Deskripsi

Merupakan tabel utama yang menyimpan riwayat akademik siswa setiap semester.

Primary Key

record_id

Foreign Key

student_id

academic_year_id

created_by

| Column | Type | Constraint |
|---------|------|------------|
| record_id | UUID | PRIMARY KEY |
| student_id | UUID | FOREIGN KEY |
| academic_year_id | UUID | FOREIGN KEY |
| average_score | DECIMAL(5,2) | |
| teacher_note | TEXT | |
| created_by | UUID | FOREIGN KEY |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

---

# DB_SUBJECT_SCORE

Deskripsi

Menyimpan nilai mata pelajaran setiap semester.

Primary Key

score_id

Foreign Key

record_id

| Column | Type | Constraint |
|---------|------|------------|
| score_id | UUID | PRIMARY KEY |
| record_id | UUID | FOREIGN KEY |
| subject_name | VARCHAR(100) | NOT NULL |
| score | DECIMAL(5,2) | NOT NULL |

---

# DB_ATTENDANCE

Deskripsi

Menyimpan rekap kehadiran siswa.

Primary Key

attendance_id

Foreign Key

record_id

| Column | Type | Constraint |
|---------|------|------------|
| attendance_id | UUID | PRIMARY KEY |
| record_id | UUID | FOREIGN KEY, UNIQUE |
| sick | INTEGER | DEFAULT 0 |
| permission | INTEGER | DEFAULT 0 |
| absent | INTEGER | DEFAULT 0 |

---

# DB_ACHIEVEMENT

Deskripsi

Menyimpan data prestasi siswa.

Primary Key

achievement_id

Foreign Key

record_id

| Column | Type | Constraint |
|---------|------|------------|
| achievement_id | UUID | PRIMARY KEY |
| record_id | UUID | FOREIGN KEY |
| title | VARCHAR(150) | NOT NULL |
| category | VARCHAR(50) | |
| description | TEXT | |

---

# DB_HEALTH_RECORD

Deskripsi

Menyimpan data kesehatan siswa.

Primary Key

health_id

Foreign Key

record_id

| Column | Type | Constraint |
|---------|------|------------|
| health_id | UUID | PRIMARY KEY |
| record_id | UUID | FOREIGN KEY, UNIQUE |
| height | DECIMAL(5,2) | |
| weight | DECIMAL(5,2) | |
| health_note | TEXT | |

---

# DB_AI_SUMMARY

Deskripsi

Menyimpan hasil keluaran Artificial Intelligence.

Primary Key

summary_id

Foreign Key

record_id

| Column | Type | Constraint |
|---------|------|------------|
| summary_id | UUID | PRIMARY KEY |
| record_id | UUID | FOREIGN KEY |
| summary_type | ENUM | NOT NULL |
| generated_text | TEXT | NOT NULL |
| generated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

---

# Relasi Foreign Key

| Parent Table | Parent Key | Child Table | Child Key |
|---------------|------------|-------------|-----------|
| DB_USER | user_id | DB_CLASS | homeroom_teacher_id |
| DB_USER | user_id | DB_SEMESTER_RECORD | created_by |
| DB_ACADEMIC_YEAR | academic_year_id | DB_CLASS | academic_year_id |
| DB_ACADEMIC_YEAR | academic_year_id | DB_SEMESTER_RECORD | academic_year_id |
| DB_CLASS | class_id | DB_STUDENT | class_id |
| DB_STUDENT | student_id | DB_SEMESTER_RECORD | student_id |
| DB_SEMESTER_RECORD | record_id | DB_SUBJECT_SCORE | record_id |
| DB_SEMESTER_RECORD | record_id | DB_ATTENDANCE | record_id |
| DB_SEMESTER_RECORD | record_id | DB_ACHIEVEMENT | record_id |
| DB_SEMESTER_RECORD | record_id | DB_HEALTH_RECORD | record_id |
| DB_SEMESTER_RECORD | record_id | DB_AI_SUMMARY | record_id |

---

# Ringkasan Tabel

| Tabel | Fungsi |
|--------|--------|
| DB_USER | Menyimpan akun pengguna sistem |
| DB_ACADEMIC_YEAR | Menyimpan tahun ajaran dan semester |
| DB_CLASS | Menyimpan data kelas dan wali kelas |
| DB_STUDENT | Menyimpan biodata siswa |
| DB_SEMESTER_RECORD | Menyimpan riwayat akademik setiap semester |
| DB_SUBJECT_SCORE | Menyimpan nilai setiap mata pelajaran |
| DB_ATTENDANCE | Menyimpan rekap kehadiran |
| DB_ACHIEVEMENT | Menyimpan prestasi siswa |
| DB_HEALTH_RECORD | Menyimpan data kesehatan siswa |
| DB_AI_SUMMARY | Menyimpan hasil ringkasan dan draft yang dihasilkan AI |

---

# Alur Penyimpanan Data

```text
User
        │
        ▼
Academic Year
        │
        ▼
Class
        │
        ▼
Student
        │
        ▼
Semester Record
        │
 ┌──────┼──────────────┬──────────────┬──────────────┐
 ▼      ▼              ▼              ▼              ▼
Subject Attendance Achievement Health AI Summary
Score
```

---

# Prinsip Desain Database

Database dirancang berdasarkan beberapa prinsip berikut.

- Satu siswa memiliki satu identitas utama yang digunakan selama masa belajar.
- Riwayat akademik disimpan per semester sehingga perkembangan siswa dapat ditelusuri secara longitudinal.
- Artificial Intelligence tidak menyimpan data akademik baru, melainkan menghasilkan ringkasan berdasarkan data yang telah tersedia.
- Database hanya menyimpan data yang benar-benar diperlukan untuk mendukung fitur MVP.
- Struktur dibuat sederhana agar mudah dikembangkan, dipahami tim, dan didemonstrasikan dalam kompetisi.

---

# Ruang Lingkup MVP

Database ini hanya mendukung fitur-fitur utama berikut.

- Login pengguna
- Manajemen kelas
- Manajemen siswa
- Riwayat akademik longitudinal
- Rekap nilai mata pelajaran
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