# Physical Entity Relationship Diagram (Physical ERD)
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Physical ERD merupakan tahap implementasi dari Logical ERD ke dalam struktur database yang akan digunakan oleh sistem.

Pada tahap ini setiap entitas direpresentasikan sebagai tabel database lengkap dengan Primary Key (PK), Foreign Key (FK), serta atribut yang akan disimpan.

Karena sistem ini merupakan MVP untuk Lomba Inovasi Digital Mahasiswa (LIDM), desain database dibuat sesederhana mungkin tanpa mengurangi kebutuhan utama sistem.

Physical ERD ini hanya ditujukan untuk mendukung demonstrasi aplikasi (Proof of Concept), bukan sebagai sistem produksi yang digunakan sekolah maupun pemerintah.

---

# DB_USER

Deskripsi

Menyimpan akun seluruh pengguna sistem.

| Field | Type | Constraint |
|--------|------|------------|
| user_id | UUID | PK |
| full_name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(100) | UNIQUE |
| password_hash | VARCHAR(255) | NOT NULL |
| role | ENUM | Administrator, Operator, Guru, Kepala Sekolah |
| is_active | BOOLEAN | DEFAULT TRUE |
| created_at | TIMESTAMP | |

---

# DB_ACADEMIC_YEAR

Deskripsi

Menyimpan informasi tahun ajaran.

| Field | Type | Constraint |
|--------|------|------------|
| academic_year_id | UUID | PK |
| academic_year | VARCHAR(20) | NOT NULL |
| semester | ENUM | Ganjil, Genap |
| is_active | BOOLEAN | |

---

# DB_CLASS

Deskripsi

Menyimpan data kelas.

| Field | Type | Constraint |
|--------|------|------------|
| class_id | UUID | PK |
| academic_year_id | UUID | FK |
| homeroom_teacher_id | UUID | FK |
| class_name | VARCHAR(20) | |
| grade | INTEGER | |

FK

academic_year_id → DB_ACADEMIC_YEAR

homeroom_teacher_id → DB_USER

---

# DB_STUDENT

Deskripsi

Menyimpan identitas siswa.

| Field | Type | Constraint |
|--------|------|------------|
| student_id | UUID | PK |
| class_id | UUID | FK |
| nis | VARCHAR(20) | |
| nisn | VARCHAR(20) | |
| full_name | VARCHAR(100) | |
| gender | ENUM | L, P |
| birth_date | DATE | |
| address | TEXT | |
| parent_name | VARCHAR(100) | |

FK

class_id → DB_CLASS

---

# DB_SEMESTER_RECORD

Deskripsi

Merupakan tabel utama sistem.

Satu baris mewakili perkembangan seorang siswa pada satu semester.

| Field | Type | Constraint |
|--------|------|------------|
| record_id | UUID | PK |
| student_id | UUID | FK |
| academic_year_id | UUID | FK |
| average_score | DECIMAL(5,2) | |
| teacher_note | TEXT | |
| created_by | UUID | FK |
| created_at | TIMESTAMP | |

FK

student_id → DB_STUDENT

academic_year_id → DB_ACADEMIC_YEAR

created_by → DB_USER

---

# DB_SUBJECT_SCORE

Deskripsi

Menyimpan nilai mata pelajaran.

| Field | Type | Constraint |
|--------|------|------------|
| score_id | UUID | PK |
| record_id | UUID | FK |
| subject_name | VARCHAR(100) | |
| score | DECIMAL(5,2) | |

FK

record_id → DB_SEMESTER_RECORD

---

# DB_ATTENDANCE

Deskripsi

Menyimpan rekap kehadiran.

| Field | Type | Constraint |
|--------|------|------------|
| attendance_id | UUID | PK |
| record_id | UUID | FK UNIQUE |
| sick | INTEGER | DEFAULT 0 |
| permission | INTEGER | DEFAULT 0 |
| absent | INTEGER | DEFAULT 0 |

FK

record_id → DB_SEMESTER_RECORD

---

# DB_ACHIEVEMENT

Deskripsi

Menyimpan prestasi siswa.

| Field | Type | Constraint |
|--------|------|------------|
| achievement_id | UUID | PK |
| record_id | UUID | FK |
| title | VARCHAR(150) | |
| category | VARCHAR(50) | |
| description | TEXT | |

FK

record_id → DB_SEMESTER_RECORD

---

# DB_HEALTH_RECORD

Deskripsi

Menyimpan data kesehatan sederhana.

| Field | Type | Constraint |
|--------|------|------------|
| health_id | UUID | PK |
| record_id | UUID | FK UNIQUE |
| height | DECIMAL(5,2) | |
| weight | DECIMAL(5,2) | |
| health_note | TEXT | |

FK

record_id → DB_SEMESTER_RECORD

---

# DB_AI_SUMMARY

Deskripsi

Menyimpan hasil keluaran Artificial Intelligence.

Data ini dapat dihasilkan ulang apabila diperlukan.

| Field | Type | Constraint |
|--------|------|------------|
| summary_id | UUID | PK |
| record_id | UUID | FK |
| summary_type | ENUM | Student Summary, Draft Description, Transition Summary |
| generated_text | TEXT | |
| generated_at | TIMESTAMP | |

FK

record_id → DB_SEMESTER_RECORD

---

# Relasi Antar Tabel

| Parent Table | Child Table | Relationship |
|--------------|-------------|--------------|
| DB_USER | DB_CLASS | 1 : N |
| DB_USER | DB_SEMESTER_RECORD | 1 : N |
| DB_ACADEMIC_YEAR | DB_CLASS | 1 : N |
| DB_ACADEMIC_YEAR | DB_SEMESTER_RECORD | 1 : N |
| DB_CLASS | DB_STUDENT | 1 : N |
| DB_STUDENT | DB_SEMESTER_RECORD | 1 : N |
| DB_SEMESTER_RECORD | DB_SUBJECT_SCORE | 1 : N |
| DB_SEMESTER_RECORD | DB_ATTENDANCE | 1 : 1 |
| DB_SEMESTER_RECORD | DB_ACHIEVEMENT | 1 : N |
| DB_SEMESTER_RECORD | DB_HEALTH_RECORD | 1 : 1 |
| DB_SEMESTER_RECORD | DB_AI_SUMMARY | 1 : N |

---

# Physical ERD

```text
                         DB_USER
                    +----------------+
                    | PK user_id     |
                    +----------------+
                           |
                    1       |      N
                           |
                    +----------------+
                    |   DB_CLASS     |
                    +----------------+
                    | PK class_id    |
                    | FK teacher_id  |
                    | FK year_id     |
                    +----------------+
                           |
                    1       |      N
                           |
                    +----------------+
                    |  DB_STUDENT    |
                    +----------------+
                    | PK student_id  |
                    | FK class_id    |
                    +----------------+
                           |
                    1       |      N
                           |
              +---------------------------+
              |  DB_SEMESTER_RECORD       |
              +---------------------------+
              | PK record_id              |
              | FK student_id             |
              | FK academic_year_id       |
              | FK created_by             |
              +---------------------------+
                 |      |      |      |
                 |      |      |      |
                 |      |      |      |
             1:N | 1:1  |1:N   |1:1
                 |      |      |
                 V      V      V
        DB_SUBJECT_SCORE
        DB_ATTENDANCE
        DB_ACHIEVEMENT
        DB_HEALTH_RECORD
                 |
                 |1:N
                 V
          DB_AI_SUMMARY

        DB_ACADEMIC_YEAR
               |
          1    |    N
               |
          DB_CLASS
```

---

# Normalisasi

Desain database telah memenuhi prinsip normalisasi dasar.

First Normal Form (1NF)

- Seluruh atribut bersifat atomik.
- Tidak terdapat data berulang dalam satu kolom.

Second Normal Form (2NF)

- Seluruh atribut bergantung sepenuhnya pada Primary Key.

Third Normal Form (3NF)

- Tidak terdapat ketergantungan transitif.
- Informasi dipisahkan ke tabel yang sesuai.

---

# Catatan Implementasi MVP

Untuk menjaga ruang lingkup tetap sederhana dan sesuai dengan kompetisi, beberapa keputusan desain diambil.

- Tidak membuat tabel Subject karena daftar mata pelajaran sedikit dan relatif tetap.
- Tidak membuat tabel Role maupun Permission terpisah.
- Tidak membuat tabel School karena sistem hanya mendukung satu sekolah sebagai studi kasus.
- Tidak membuat tabel Notification, Activity Log, Audit Trail, maupun Session.
- Tidak membuat tabel Prompt AI, Embedding, Vector Database, maupun RAG karena fitur tersebut merupakan rencana pengembangan lanjutan.
- Hasil AI hanya disimpan sebagai teks pada tabel DB_AI_SUMMARY sehingga implementasi tetap sederhana namun cukup untuk mendemonstrasikan kemampuan Artificial Intelligence pada MVP.

Dengan struktur ini, database sudah mampu mendukung seluruh fitur MVP, yaitu pengelolaan riwayat akademik longitudinal, penyusunan administrasi Buku Induk, serta fitur AI Student Summary, AI Draft Deskripsi, dan AI Student Transition Summary tanpa kompleksitas yang tidak diperlukan.