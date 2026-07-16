# Logical Entity Relationship Diagram (Logical ERD)
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Logical ERD merupakan pengembangan dari Conceptual ERD dengan menambahkan atribut utama, Primary Key (PK), Foreign Key (FK), serta hubungan antar entitas.

Pada tahap ini desain masih bersifat logis (logical design), sehingga belum membahas tipe data database, indexing, trigger, ataupun optimasi performa.

Karena sistem ini merupakan MVP untuk LIDM, desain dibuat sederhana, mudah dipahami, dan hanya memuat kebutuhan inti sistem.

---

# Daftar Entitas

## 1. User

Deskripsi

Menyimpan seluruh pengguna sistem.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| user_id | PK | ID pengguna |
| full_name | | Nama lengkap |
| email | | Email |
| password | | Password |
| role | | Administrator, Operator, Guru, Kepala Sekolah |
| is_active | | Status akun |

---

## 2. Academic Year

Deskripsi

Menyimpan informasi tahun ajaran.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| academic_year_id | PK | ID Tahun Ajaran |
| academic_year | | Contoh 2026/2027 |
| semester | | Ganjil / Genap |
| is_active | | Semester aktif |

---

## 3. Class

Deskripsi

Menyimpan data kelas.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| class_id | PK | ID Kelas |
| academic_year_id | FK | Tahun Ajaran |
| homeroom_teacher_id | FK | Guru Wali Kelas |
| class_name | | Contoh 5A |
| grade | | Tingkat kelas |

---

## 4. Student

Deskripsi

Menyimpan identitas siswa.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| student_id | PK | ID Siswa |
| class_id | FK | Kelas aktif |
| nis | | Nomor Induk Sekolah |
| nisn | | Nomor Induk Nasional |
| full_name | | Nama siswa |
| gender | | Jenis kelamin |
| birth_date | | Tanggal lahir |
| address | | Alamat |
| parent_name | | Nama orang tua |

---

## 5. Semester Record

Deskripsi

Merupakan inti sistem Longitudinal Student Academic Record.

Setiap semester siswa memiliki satu record.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| record_id | PK | ID Record |
| student_id | FK | Siswa |
| academic_year_id | FK | Tahun Ajaran |
| average_score | | Nilai rata-rata |
| teacher_note | | Catatan perkembangan |
| created_by | FK | Guru yang mengisi |

---

## 6. Subject Score

Deskripsi

Menyimpan nilai tiap mata pelajaran.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| score_id | PK | ID Nilai |
| record_id | FK | Semester Record |
| subject_name | | Nama Mata Pelajaran |
| score | | Nilai |

Catatan

Untuk MVP, nama mata pelajaran masih disimpan langsung tanpa membuat tabel Subject terpisah agar desain tetap sederhana.

---

## 7. Attendance

Deskripsi

Menyimpan rekap kehadiran.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| attendance_id | PK | ID Kehadiran |
| record_id | FK | Semester Record |
| sick | | Jumlah sakit |
| permission | | Jumlah izin |
| absent | | Jumlah alpha |

---

## 8. Achievement

Deskripsi

Menyimpan prestasi siswa.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| achievement_id | PK | ID Prestasi |
| record_id | FK | Semester Record |
| title | | Nama prestasi |
| category | | Akademik / Non Akademik |
| description | | Deskripsi singkat |

---

## 9. Health Record

Deskripsi

Menyimpan informasi kesehatan sederhana.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| health_id | PK | ID Kesehatan |
| record_id | FK | Semester Record |
| height | | Tinggi badan |
| weight | | Berat badan |
| health_note | | Riwayat kesehatan |

---

## 10. AI Summary

Deskripsi

Menyimpan hasil keluaran Artificial Intelligence.

AI tidak menyimpan data akademik baru.

AI hanya menghasilkan narasi berdasarkan data yang sudah tersedia.

### Attributes

| Attribute | Key | Keterangan |
|------------|-----|------------|
| summary_id | PK | ID Summary |
| record_id | FK | Semester Record |
| summary_type | | Student Summary, Draft Deskripsi, Transition Summary |
| generated_text | | Hasil AI |
| generated_at | | Waktu pembuatan |

---

# Relationship

## Academic Year → Class

Relationship

1 : N

Satu tahun ajaran memiliki banyak kelas.

---

## User → Class

Relationship

1 : N

Satu guru dapat menjadi wali kelas pada beberapa tahun ajaran yang berbeda.

---

## Class → Student

Relationship

1 : N

Satu kelas memiliki banyak siswa.

---

## Student → Semester Record

Relationship

1 : N

Satu siswa memiliki banyak Semester Record.

Inilah yang membentuk Longitudinal Student Academic Record.

---

## Academic Year → Semester Record

Relationship

1 : N

Satu tahun ajaran memiliki banyak Semester Record.

---

## Semester Record → Subject Score

Relationship

1 : N

Setiap Semester Record memiliki banyak nilai mata pelajaran.

---

## Semester Record → Attendance

Relationship

1 : 1

Satu Semester Record memiliki satu rekap kehadiran.

---

## Semester Record → Achievement

Relationship

1 : N

Satu Semester Record dapat memiliki banyak prestasi.

---

## Semester Record → Health Record

Relationship

1 : 1

Satu Semester Record memiliki satu data kesehatan.

---

## Semester Record → AI Summary

Relationship

1 : N

Satu Semester Record dapat menghasilkan beberapa keluaran AI.

Contoh

- Student Summary
- Draft Deskripsi
- Transition Summary

---

# Logical ERD

```text
+------------------+
| User             |
+------------------+
| PK user_id       |
| full_name        |
| email            |
| password         |
| role             |
| is_active        |
+------------------+
         |
         | 1
         |
         | N
+----------------------+
| Class                |
+----------------------+
| PK class_id          |
| FK academic_year_id  |
| FK homeroom_teacher  |
| class_name           |
| grade                |
+----------------------+
         |
         | 1
         |
         | N
+----------------------+
| Student              |
+----------------------+
| PK student_id        |
| FK class_id          |
| nis                  |
| nisn                 |
| full_name            |
| gender               |
| birth_date           |
| address              |
| parent_name          |
+----------------------+
         |
         | 1
         |
         | N
+----------------------------+
| Semester Record            |
+----------------------------+
| PK record_id               |
| FK student_id              |
| FK academic_year_id        |
| average_score              |
| teacher_note               |
| FK created_by              |
+----------------------------+
   |      |       |        |
   |      |       |        |
   |      |       |        |
   |      |       |        |
   V      V       V        V
Subject  Attendance Achievement Health
 Score                         Record
   |
   |
   V
AI Summary

Academic Year
      |
      | 1
      |
      | N
     Class
```

---

# Ringkasan Kardinalitas

| Relationship | Kardinalitas |
|--------------|--------------|
| Academic Year → Class | 1 : N |
| User → Class | 1 : N |
| Class → Student | 1 : N |
| Student → Semester Record | 1 : N |
| Academic Year → Semester Record | 1 : N |
| Semester Record → Subject Score | 1 : N |
| Semester Record → Attendance | 1 : 1 |
| Semester Record → Achievement | 1 : N |
| Semester Record → Health Record | 1 : 1 |
| Semester Record → AI Summary | 1 : N |

---

# Catatan Desain MVP

Beberapa penyederhanaan sengaja dilakukan agar ruang lingkup tetap sesuai dengan kompetisi LIDM.

Penyederhanaan tersebut meliputi:

- Tidak membuat tabel Subject karena daftar mata pelajaran relatif sedikit dan stabil.
- Tidak membuat tabel Role terpisah karena hanya terdapat empat peran pengguna.
- Tidak membuat tabel Permission karena kontrol akses cukup berdasarkan role dan penugasan wali kelas.
- Tidak membuat tabel School karena sistem hanya digunakan untuk demonstrasi pada satu sekolah.
- Tidak membuat tabel Audit Log, Notification, maupun Activity History karena belum dibutuhkan pada MVP.
- Tidak menyimpan embedding, prompt, maupun metadata AI karena fitur RAG dan Vector Database merupakan rencana pengembangan lanjutan.

Dengan struktur ini, Logical ERD tetap sederhana namun sudah mampu mendukung seluruh fitur MVP, yaitu pengelolaan riwayat akademik longitudinal, penyusunan administrasi Buku Induk, serta tiga fitur AI utama (Student Summary, Draft Deskripsi, dan Student Transition Summary).