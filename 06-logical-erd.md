# Logical Entity Relationship Diagram (Logical ERD)
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Logical ERD merupakan pengembangan dari Conceptual ERD dengan menambahkan atribut utama, Primary Key (PK), Foreign Key (FK), serta hubungan antar entitas.

Dokumen ini telah diselaraskan dengan implementasi Prisma ORM yang digunakan dalam sistem.

Karena sistem ini merupakan MVP untuk LIDM, desain dibuat sederhana, mudah dipahami, dan hanya memuat kebutuhan inti sistem.

---

# Daftar Entitas

## 1. User

Deskripsi: Menyimpan seluruh pengguna sistem.

| Attribute | Key | Keterangan                       |
|-----------|-----|----------------------------------|
| user_id   | PK  | ID pengguna (UUID)               |
| username  |     | Nama pengguna (UNIQUE)           |
| password  |     | Password ter-hash                |
| name      |     | Nama lengkap                     |
| role      |     | ADMINISTRATOR, OPERATOR_SEKOLAH, GURU, KEPALA_SEKOLAH |
| is_active |     | Status akun aktif/nonaktif       |

---

## 2. Academic Year

Deskripsi: Menyimpan informasi tahun ajaran.

| Attribute       | Key | Keterangan           |
|-----------------|-----|----------------------|
| academic_year_id| PK  | ID Tahun Ajaran      |
| year            |     | Contoh "2025/2026"   |
| is_active       |     | Status aktif         |
| is_archived     |     | Status arsip         |

Catatan: Field `semester` tidak disimpan di entitas ini melainkan di SemesterRecord.

---

## 3. Class

Deskripsi: Menyimpan data kelas dan wali kelas.

| Attribute          | Key | Keterangan               |
|--------------------|-----|--------------------------|
| class_id           | PK  | ID Kelas                 |
| academic_year_id   | FK  | Tahun Ajaran             |
| homeroom_teacher_id| FK  | Guru Wali Kelas (nullable)|
| name               |     | Contoh "Kelas 4A"        |

Constraint: `(name, academic_year_id)` unique.

---

## 4. Student

Deskripsi: Menyimpan identitas siswa.

| Attribute | Key | Keterangan               |
|-----------|-----|--------------------------|
| student_id| PK  | ID Siswa                 |
| class_id  | FK  | Kelas aktif              |
| nis       |     | Nomor Induk Sekolah (UNIQUE) |
| nisn      |     | Nomor Induk Nasional (UNIQUE)|
| name      |     | Nama siswa               |
| gender    |     | Jenis kelamin            |

---

## 5. Semester Record

Deskripsi: Merupakan inti sistem Longitudinal Student Academic Record.
Setiap semester siswa memiliki satu record.

| Attribute        | Key | Keterangan                    |
|------------------|-----|-------------------------------|
| record_id        | PK  | ID Record                     |
| student_id       | FK  | Siswa                         |
| academic_year_id | FK  | Tahun Ajaran                  |
| semester         |     | 1 = Ganjil, 2 = Genap         |
| created_by       | FK  | Guru yang mengisi             |

Constraint: `(student_id, academic_year_id, semester)` unique.

---

## 6. Subject Score

Deskripsi: Menyimpan nilai tiap mata pelajaran.

| Attribute        | Key | Keterangan                    |
|------------------|-----|-------------------------------|
| score_id         | PK  | ID Nilai                      |
| record_id        | FK  | Semester Record               |
| subject_name     |     | Nama Mata Pelajaran           |
| knowledge_score  |     | Nilai Pengetahuan             |
| skills_score     |     | Nilai Keterampilan            |
| notes            |     | Catatan (opsional)            |

Constraint: `(record_id, subject_name)` unique.
Catatan: Nilai dipisah menjadi knowledge dan skills sesuai Kurikulum Merdeka.

---

## 7. Attendance

Deskripsi: Menyimpan rekap kehadiran.

| Attribute    | Key | Keterangan                  |
|--------------|-----|-----------------------------|
| attendance_id| PK  | ID Kehadiran                |
| record_id    | FK  | Semester Record (UNIQUE)    |
| sick         |     | Jumlah sakit                |
| permission   |     | Jumlah izin                 |
| absent       |     | Jumlah alpha                |

---

## 8. Achievement

Deskripsi: Menyimpan prestasi siswa.

| Attribute      | Key | Keterangan                  |
|----------------|-----|-----------------------------|
| achievement_id | PK  | ID Prestasi                 |
| record_id      | FK  | Semester Record             |
| title          |     | Nama prestasi               |
| type           |     | Akademik / Non Akademik     |
| description    |     | Deskripsi singkat (opsional)|

---

## 9. Health Record

Deskripsi: Menyimpan informasi kesehatan sederhana.

| Attribute        | Key | Keterangan                  |
|------------------|-----|-----------------------------|
| health_id        | PK  | ID Kesehatan                |
| record_id        | FK  | Semester Record (UNIQUE)    |
| height           |     | Tinggi badan (cm)           |
| weight           |     | Berat badan (kg)            |
| hearing_condition|     | Kondisi pendengaran         |
| vision_condition |     | Kondisi penglihatan         |
| teeth_condition  |     | Kondisi gigi                |

---

## 10. Class Audit Log

Deskripsi: Menyimpan log perubahan wali kelas.

| Attribute           | Key  | Keterangan                      |
|---------------------|------|---------------------------------|
| audit_id            | PK   | ID Log                          |
| class_id            | FK   | Kelas                           |
| previous_teacher_id | FK   | Wali kelas sebelumnya (nullable)|
| new_teacher_id      | FK   | Wali kelas baru (nullable)      |
| changed_by          | FK   | Admin yang mengubah             |
| changed_at          |      | Waktu perubahan                 |

---

## 11. AI Summary

Deskripsi: Menyimpan hasil keluaran Artificial Intelligence.

| Attribute       | Key | Keterangan                     |
|-----------------|-----|--------------------------------|
| summary_id      | PK  | ID Summary                     |
| record_id       | FK  | Semester Record                |
| summary_type    |     | STUDENT_SUMMARY, DRAFT_DESCRIPTION, TRANSITION_SUMMARY |
| content         |     | Hasil AI (teks)                |
| is_final        |     | Status finalisasi oleh guru    |
| version         |     | Versi draft                    |

Constraint: `(record_id, summary_type, version)` unique.

---

# Hubungan Antar Entitas

| Entitas 1           | Entitas 2           | Kardinalitas | Keterangan                                   |
|---------------------|----------------------|--------------|----------------------------------------------|
| User                | Class                | 1 : N        | Satu guru jadi wali kelas beberapa kelas      |
| Academic Year       | Class                | 1 : N        | Satu tahun ajaran punya banyak kelas          |
| Class               | Student              | 1 : N        | Satu kelas punya banyak siswa                 |
| User                | Semester Record      | 1 : N        | Satu guru membuat banyak record               |
| Academic Year       | Semester Record      | 1 : N        | Satu tahun ajaran punya banyak record         |
| Student             | Semester Record      | 1 : N        | Satu siswa punya banyak record (longitudinal) |
| Semester Record     | Subject Score        | 1 : N        | Satu record punya banyak nilai mapel          |
| Semester Record     | Attendance           | 1 : 1        | Satu record punya satu data kehadiran (upsert)|
| Semester Record     | Achievement          | 1 : N        | Satu record punya banyak prestasi             |
| Semester Record     | Health Record        | 1 : 1        | Satu record punya satu data kesehatan (upsert)|
| Semester Record     | AI Summary           | 1 : N        | Satu record punya beberapa hasil AI           |
| Class               | Class Audit Log      | 1 : N        | Satu kelas punya banyak log perubahan        |
| User                | Class Audit Log      | 1 : N        | Satu admin membuat banyak log                |

---

# Ringkasan Entitas

| Entitas         | Fungsi                                          |
|-----------------|-------------------------------------------------|
| User            | Pengguna sistem                                |
| Academic Year   | Mengelola tahun ajaran                         |
| Class           | Mengelompokkan siswa dan wali kelas             |
| Student         | Menyimpan identitas siswa                      |
| Semester Record | Menyimpan riwayat akademik setiap semester     |
| Subject Score   | Menyimpan nilai mata pelajaran (knowledge + skill)|
| Attendance      | Menyimpan data kehadiran                       |
| Achievement     | Menyimpan prestasi siswa                       |
| Health Record   | Menyimpan data kesehatan siswa                 |
| Class Audit Log | Menyimpan log perubahan wali kelas             |
| AI Summary      | Menyimpan hasil analisis AI                    |

---

# Catatan Desain MVP

Beberapa penyederhanaan sengaja dilakukan agar ruang lingkup tetap sesuai dengan kompetisi LIDM:

- Tidak membuat tabel Subject karena daftar mata pelajaran relatif sedikit dan stabil.
- Tidak membuat tabel Role terpisah karena hanya terdapat empat peran pengguna (Enum).
- Tidak membuat tabel School karena sistem hanya digunakan untuk demonstrasi pada satu sekolah.
- Tidak membuat tabel Audit Log general — hanya ClassAuditLog untuk perubahan teacher assignment.
- Tidak menyimpan embedding, prompt, maupun metadata AI karena fitur RAG dan Vector Database merupakan rencana pengembangan lanjutan.

Dengan struktur ini, Logical ERD sudah mampu mendukung seluruh fitur MVP, yaitu pengelolaan riwayat akademik longitudinal, penyusunan administrasi Buku Induk, serta tiga fitur AI utama.
