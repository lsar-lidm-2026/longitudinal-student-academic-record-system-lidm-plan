# Conceptual Entity Relationship Diagram (Conceptual ERD)
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

## Pendahuluan

Conceptual ERD bertujuan menggambarkan entitas utama yang dibutuhkan sistem beserta hubungan antar entitas secara konseptual.

Pada tahap ini, ERD belum membahas atribut teknis maupun implementasi database. Fokus utama adalah memahami objek bisnis yang terlibat dalam sistem.

Karena sistem ini merupakan MVP untuk kebutuhan LIDM, model data dibuat sesederhana mungkin tanpa menghilangkan kebutuhan utama sistem.

---

# Tujuan Conceptual ERD

Conceptual ERD dirancang untuk mendukung kebutuhan berikut.

- Mengelola data siswa.
- Mengelola data guru.
- Mengelola tahun ajaran dan semester.
- Menyimpan riwayat akademik siswa secara longitudinal.
- Mendukung penyusunan Buku Induk.
- Mendukung fitur Artificial Intelligence.

---

# Daftar Entitas

## 1. User

Merepresentasikan seluruh pengguna sistem.

Contoh

- Administrator
- Operator Sekolah
- Guru
- Kepala Sekolah

Fungsi

- Login ke sistem.
- Mengakses fitur sesuai hak akses.

---

## 2. Class

Merepresentasikan kelas pada suatu tahun ajaran.

Contoh

- 1A
- 2B
- 5A

Fungsi

- Mengelompokkan siswa.
- Menentukan wali kelas.

---

## 3. Student

Merepresentasikan identitas dasar siswa.

Fungsi

- Menyimpan biodata siswa.
- Menjadi pusat seluruh riwayat akademik.

---

## 4. Academic Year

Merepresentasikan tahun ajaran.

Contoh

- 2026/2027
- 2027/2028

Fungsi

- Menjadi acuan seluruh aktivitas akademik.

---

## 5. Semester Record

Merepresentasikan riwayat akademik siswa pada satu semester.

Entitas ini merupakan inti dari sistem Longitudinal Student Academic Record.

Setiap semester akan menghasilkan satu record baru.

Informasi yang disimpan meliputi.

- Nilai
- Kehadiran
- Prestasi
- Data kesehatan
- Deskripsi perkembangan

---

## 6. Subject Score

Merepresentasikan nilai mata pelajaran siswa.

Contoh

- Matematika
- Bahasa Indonesia
- IPA
- IPS

Fungsi

- Menyimpan nilai akademik setiap semester.

---

## 7. Attendance

Merepresentasikan data kehadiran siswa.

Contoh

- Hadir
- Izin
- Sakit
- Alpha

---

## 8. Achievement

Merepresentasikan prestasi siswa.

Contoh

- Akademik
- Non Akademik

---

## 9. Health Record

Merepresentasikan data kesehatan sederhana.

Contoh

- Tinggi badan
- Berat badan
- Riwayat kesehatan

Data ini dibutuhkan karena menjadi bagian dari Buku Induk.

---

## 10. AI Summary

Merepresentasikan hasil analisis Artificial Intelligence.

Contoh

- Student Summary
- Draft Deskripsi
- Student Transition Summary

AI Summary tidak menggantikan data akademik.

AI hanya menghasilkan ringkasan berdasarkan data yang sudah tersedia.

---

## 11. Class Audit Log

Merepresentasikan catatan perubahan wali kelas.

Fungsi

- Mencatat siapa yang mengubah wali kelas
- Mencatat wali kelas lama dan baru
- Mendukung akuntabilitas perubahan hak akses

---

# Hubungan Antar Entitas

## User — Class

Relasi

Satu guru dapat menjadi wali dari beberapa kelas pada tahun ajaran yang berbeda.

Satu kelas hanya memiliki satu wali kelas aktif.

Relationship: 1 : N

---

## User — Class Audit Log

Relasi

Satu administrator dapat membuat banyak log perubahan wali kelas.

Relationship: 1 : N

---

## Class — Class Audit Log

Relasi

Satu kelas memiliki banyak log perubahan wali kelas.

Relationship: 1 : N

---

## Academic Year — Class

Satu tahun ajaran memiliki banyak kelas.

Relationship

Academic Year

1

──────

N

Class

---

## Class — Student

Satu kelas memiliki banyak siswa.

Seorang siswa hanya berada pada satu kelas dalam satu tahun ajaran.

Relationship

Class

1

──────

N

Student

---

## Student — Semester Record

Satu siswa memiliki banyak riwayat semester.

Inilah konsep utama Longitudinal Student Academic Record.

Relationship

Student

1

──────

N

Semester Record

---

## Academic Year — Semester Record

Satu tahun ajaran menghasilkan banyak Semester Record.

Relationship

Academic Year

1

──────

N

Semester Record

---

## Semester Record — Subject Score

Satu Semester Record memiliki banyak nilai mata pelajaran.

Relationship

Semester Record

1

──────

N

Subject Score

---

## Semester Record — Attendance

Setiap Semester Record memiliki satu data kehadiran.

Relationship

Semester Record

1

──────

1

Attendance

---

## Semester Record — Achievement

Satu Semester Record dapat memiliki beberapa prestasi.

Relationship

Semester Record

1

──────

N

Achievement

---

## Semester Record — Health Record

Setiap Semester Record memiliki satu data kesehatan.

Relationship

Semester Record

1

──────

1

Health Record

---

## Semester Record — AI Summary

Satu Semester Record dapat menghasilkan beberapa keluaran AI.

Contoh

- Student Summary
- Draft Deskripsi

Relationship

Semester Record

1

──────

N

AI Summary

---

# Gambaran Conceptual ERD

```text
                +------------------+
                |       User       |
                +------------------+
                         |
                         | 1
                         |
                         | N
                +------------------+
                |      Class       |
                +------------------+
                         |
                         | 1
                         |
                         | N
                +------------------+
                |     Student      |
                +------------------+
                         |
                         | 1
                         |
                         | N
             +---------------------------+
             |     Semester Record       |
             +---------------------------+
              |      |       |      |      |
              |      |       |      |      |
              |      |       |      |      |
              V      V       V      V      V
      +---------+ +---------+ +-----------+ +-----------+ +------------+
      |Subject  | |Attendance| |Achievement| |Health     | |AI Summary |
      | Score   | |          | |           | | Record    | |            |
      +---------+ +---------+ +-----------+ +-----------+ +------------+

                ^
                |
                |
        +------------------+
        | Academic Year    |
        +------------------+
                |
                +------------------------+
```

---

# Ringkasan Entitas

| Entitas | Fungsi |
|----------|--------|
| User | Pengguna sistem |
| Academic Year | Mengelola tahun ajaran |
| Class | Mengelompokkan siswa dan wali kelas |
| Student | Menyimpan identitas siswa |
| Semester Record | Menyimpan riwayat akademik setiap semester |
| Subject Score | Menyimpan nilai mata pelajaran |
| Attendance | Menyimpan data kehadiran |
| Achievement | Menyimpan prestasi siswa |
| Health Record | Menyimpan data kesehatan siswa |
| Class Audit Log | Menyimpan log perubahan wali kelas |
| AI Summary | Menyimpan hasil analisis AI |

---

# Ruang Lingkup MVP

Conceptual ERD ini hanya mencakup kebutuhan inti untuk demonstrasi sistem pada LIDM.

Belum mencakup fitur pengembangan lanjutan seperti:

- Chatbot AI
- RAG (Retrieval-Augmented Generation)
- Vector Database
- Prediksi Prestasi Siswa
- Early Warning System
- Dashboard Orang Tua
- Notifikasi Otomatis

Entitas yang berkaitan dengan fitur-fitur tersebut akan ditambahkan pada tahap pengembangan selanjutnya agar desain MVP tetap sederhana, mudah dipahami, dan fokus pada penyelesaian masalah utama yang telah diidentifikasi.