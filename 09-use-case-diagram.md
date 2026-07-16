# Use Case Diagram
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Use Case Diagram digunakan untuk menggambarkan interaksi antara pengguna (aktor) dengan sistem.

Diagram ini menunjukkan fungsi-fungsi utama yang dapat dilakukan oleh setiap pengguna sesuai dengan hak aksesnya.

Karena sistem ini merupakan Minimum Viable Product (MVP) untuk Lomba Inovasi Digital Mahasiswa (LIDM), use case hanya mencakup kebutuhan utama sistem.

---

# Daftar Aktor

## 1. Administrator

Bertanggung jawab mengelola akun pengguna dan pengaturan sistem.

Hak akses:

- Mengelola pengguna
- Mengelola tahun ajaran
- Mengelola kelas
- Mengelola penugasan wali kelas

---

## 2. Operator Sekolah

Bertanggung jawab mengelola data master siswa.

Hak akses:

- Mengelola data siswa
- Mengelola perpindahan siswa
- Mengelola data kelas

---

## 3. Wali Kelas

Merupakan pengguna utama sistem.

Hak akses:

- Mengelola data akademik siswa
- Mengelola nilai
- Mengelola kehadiran
- Mengelola data kesehatan
- Mengelola prestasi
- Melihat riwayat siswa
- Menghasilkan ringkasan AI
- Menghasilkan draft deskripsi rapor
- Menghasilkan Student Transition Summary
- Melihat preview Buku Induk

---

## 4. Kepala Sekolah

Berfungsi sebagai monitoring.

Hak akses:

- Melihat dashboard
- Melihat riwayat akademik siswa
- Melihat preview Buku Induk

---

# Daftar Use Case

## UC-01 Login

Aktor

- Administrator
- Operator
- Wali Kelas
- Kepala Sekolah

Deskripsi

Pengguna masuk ke dalam sistem.

---

## UC-02 Kelola Pengguna

Aktor

Administrator

Deskripsi

Menambah, mengubah, dan menonaktifkan akun pengguna.

---

## UC-03 Kelola Kelas dan Tahun Ajaran

Aktor

Administrator

Deskripsi

Mengatur kelas, tahun ajaran, dan wali kelas.

---

## UC-04 Kelola Data Siswa

Aktor

Operator

Deskripsi

Mengelola identitas siswa.

---

## UC-05 Input Data Akademik

Aktor

Wali Kelas

Deskripsi

Menginput nilai, kehadiran, prestasi, kesehatan, dan catatan guru.

---

## UC-06 Melihat Riwayat Akademik Siswa

Aktor

Wali Kelas

Kepala Sekolah

Deskripsi

Menampilkan perkembangan siswa secara longitudinal.

---

## UC-07 Generate AI Student Summary

Aktor

Wali Kelas

Deskripsi

Menghasilkan ringkasan perkembangan siswa berdasarkan data akademik.

---

## UC-08 Generate AI Draft Deskripsi

Aktor

Wali Kelas

Deskripsi

Menghasilkan draft deskripsi rapor berdasarkan nilai siswa.

---

## UC-09 Generate AI Student Transition Summary

Aktor

Wali Kelas

Deskripsi

Menghasilkan ringkasan siswa yang akan diteruskan kepada wali kelas berikutnya.

---

## UC-10 Preview Buku Induk

Aktor

Wali Kelas

Kepala Sekolah

Deskripsi

Menampilkan data administrasi yang siap dipindahkan ke Buku Induk manual.

---

## UC-11 Logout

Aktor

Semua pengguna

Deskripsi

Keluar dari sistem.

---

# Hubungan Antar Use Case

Generate AI Student Summary

<<include>>

Melihat Riwayat Akademik Siswa

---

Generate AI Draft Deskripsi

<<include>>

Input Data Akademik

---

Generate AI Student Transition Summary

<<include>>

Melihat Riwayat Akademik Siswa

---

Preview Buku Induk

<<include>>

Input Data Akademik

---

# Ringkasan Aktor

| Aktor | Jumlah Use Case |
|--------|-----------------|
| Administrator | 3 |
| Operator | 2 |
| Wali Kelas | 7 |
| Kepala Sekolah | 3 |