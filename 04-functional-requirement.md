# Functional Requirements
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

## Pendahuluan

Functional Requirement mendefinisikan fungsi-fungsi utama yang harus dimiliki sistem agar mampu menyelesaikan permasalahan yang telah diidentifikasi melalui observasi dan wawancara.

Dokumen ini disusun khusus untuk kebutuhan MVP (Minimum Viable Product) pada Lomba Inovasi Teknologi Digital Pendidikan (LIDM), sehingga hanya mencakup fitur-fitur inti yang mendukung pembuktian konsep (Proof of Concept) dan demonstrasi sistem.

Sistem tidak bertujuan menggantikan Dapodik maupun Buku Induk Siswa, melainkan membantu guru dalam mengelola riwayat akademik siswa serta mempersiapkan administrasi penyusunan Buku Induk.

---

# Aktor Sistem

Sistem memiliki empat jenis pengguna.

1. Administrator
2. Operator Sekolah
3. Guru / Wali Kelas
4. Kepala Sekolah

Catatan

Pada MVP, Guru dan Wali Kelas menggunakan akun yang sama. Hak akses terhadap siswa ditentukan berdasarkan penugasan wali kelas (Teacher Assignment).

---

# FR-01 Authentication

Deskripsi

Sistem harus menyediakan proses autentikasi pengguna.

Fungsi

- Login
- Logout
- Mengelola sesi pengguna
- Membatasi akses berdasarkan peran

---

# FR-02 Academic Year Management

Deskripsi

Operator dapat mengelola tahun ajaran yang aktif.

Fungsi

- Membuat tahun ajaran
- Menentukan semester aktif
- Mengarsipkan tahun ajaran sebelumnya

Catatan

Pada MVP tidak perlu fitur kenaikan kelas otomatis.

---

# FR-03 Teacher Assignment

Deskripsi

Operator dapat menentukan guru yang menjadi wali kelas.

Fungsi

- Menentukan wali kelas
- Mengubah wali kelas
- Menampilkan daftar wali kelas aktif

Hak akses siswa mengikuti penugasan tersebut.

---

# FR-04 Student Management

Deskripsi

Operator dapat mengelola data dasar siswa.

Fungsi

- Menambah siswa
- Mengubah data siswa
- Melihat data siswa
- Mengelompokkan siswa berdasarkan kelas

Data meliputi

- Nama
- NIS
- NISN
- Jenis Kelamin
- Tanggal Lahir
- Orang Tua
- Alamat
- Kelas

---

# FR-05 Student Academic Record

Deskripsi

Guru dapat mengelola riwayat akademik siswa setiap semester.

Fungsi

- Menambahkan nilai
- Menambahkan kehadiran
- Menambahkan prestasi
- Menambahkan data kesehatan
- Menambahkan deskripsi perkembangan

Seluruh data akan tersimpan berdasarkan semester.

---

# FR-06 Longitudinal Student Profile

Deskripsi

Guru dapat melihat seluruh riwayat akademik seorang siswa dalam satu halaman.

Informasi yang ditampilkan

- Biodata
- Riwayat kelas
- Nilai setiap semester
- Kehadiran
- Prestasi
- Data kesehatan
- Deskripsi perkembangan

Tujuan

Membantu guru memahami perkembangan siswa secara menyeluruh.

---

# FR-07 Student Timeline

Deskripsi

Sistem menampilkan timeline perkembangan siswa dari kelas 1 hingga kelas 6.

Timeline terdiri dari

- Tahun Ajaran
- Semester
- Kelas

Guru dapat memilih semester tertentu untuk melihat detail perkembangan siswa.

---

# FR-08 Administrative Preparation Workspace

Deskripsi

Sistem menyediakan halaman yang mengumpulkan seluruh informasi yang diperlukan dalam penyusunan Buku Induk.

Data yang ditampilkan

- Biodata
- Nilai
- Kehadiran
- Prestasi
- Kesehatan
- Deskripsi

Tujuan

Mengurangi kebutuhan membuka banyak dokumen.

---

# FR-09 Administrative Validation

Deskripsi

Sistem memeriksa kelengkapan data administrasi.

Contoh

- Nilai belum lengkap
- Kehadiran belum lengkap
- Data kesehatan belum diisi

Tujuan

Membantu guru sebelum menyusun Buku Induk.

---

# FR-10 Preview Buku Induk

Deskripsi

Sistem menampilkan seluruh informasi sesuai urutan pengisian Buku Induk.

Catatan

Sistem tidak membuat Buku Induk Digital.

Guru tetap menulis Buku Induk secara manual.

Preview hanya digunakan sebagai panduan.

---

# FR-11 AI Student Summary

Deskripsi

Guru dapat menghasilkan ringkasan perkembangan siswa menggunakan Artificial Intelligence.

Input

- Nilai
- Kehadiran
- Prestasi
- Deskripsi semester sebelumnya

Output

Ringkasan perkembangan siswa dalam bentuk narasi singkat.

Guru dapat melakukan revisi apabila diperlukan.

---

# FR-12 AI Draft Deskripsi

Deskripsi

Artificial Intelligence membantu menyusun draft deskripsi rapor.

Input

- Nilai
- Prestasi
- Kehadiran
- Deskripsi sebelumnya

Output

Draft deskripsi yang dapat diedit oleh guru.

AI hanya berperan sebagai asisten.

---

# FR-13 AI Student Transition Summary

Deskripsi

Ketika wali kelas berubah, sistem dapat menghasilkan ringkasan perkembangan setiap siswa.

Informasi yang dirangkum

- Kekuatan akademik
- Mata pelajaran yang perlu perhatian
- Kehadiran
- Prestasi
- Catatan perkembangan

Tujuan

Membantu wali kelas baru memahami kondisi siswa lebih cepat.

---

# FR-14 Dashboard

Deskripsi

Sistem menyediakan dashboard sederhana.

Informasi yang ditampilkan

- Jumlah siswa
- Jumlah kelas
- Semester aktif
- Daftar siswa pada kelas yang diampu
- Status kelengkapan administrasi

Dashboard tidak menampilkan analitik kompleks.

---

# Ringkasan Functional Requirements

| Kode | Functional Requirement |
|------|-------------------------|
| FR-01 | Authentication |
| FR-02 | Academic Year Management |
| FR-03 | Teacher Assignment |
| FR-04 | Student Management |
| FR-05 | Student Academic Record |
| FR-06 | Longitudinal Student Profile |
| FR-07 | Student Timeline |
| FR-08 | Administrative Preparation Workspace |
| FR-09 | Administrative Validation |
| FR-10 | Preview Buku Induk |
| FR-11 | AI Student Summary |
| FR-12 | AI Draft Deskripsi |
| FR-13 | AI Student Transition Summary |
| FR-14 | Dashboard |

---

# Ruang Lingkup MVP

Fitur berikut tidak termasuk dalam MVP dan menjadi rencana pengembangan selanjutnya.

- Chatbot AI
- RAG (Retrieval-Augmented Generation)
- Vector Database
- Prediksi Prestasi Siswa
- Early Warning System
- Dashboard Orang Tua
- Notifikasi Otomatis