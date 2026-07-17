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

---

# FR-15 ML Trend Prediction

Deskripsi

Sistem dapat memprediksi tren nilai siswa berdasarkan data historis.

Fungsi

- Menampilkan grafik tren nilai per mata pelajaran
- Menampilkan proyeksi nilai semester depan
- Notifikasi jika diprediksi terjadi penurunan signifikan
- Menyediakan confidence interval untuk setiap prediksi

Target

MVP: Rule-based trend (membandingkan delta antar semester)
Post-MVP: Model regresi ML

---

# FR-16 ML Early Warning System

Deskripsi

Sistem dapat mengidentifikasi siswa yang berisiko mengalami kesulitan akademik.

Fungsi

- Mengklasifikasikan siswa ke level risiko: Aman, Waspada, Kritis
- Menampilkan faktor kontributor utama risiko
- Memberikan rekomendasi tindakan awal
- Menampilkan heatmap risiko per kelas

Target

MVP: Rule-based berdasarkan threshold nilai + kehadiran
Post-MVP: Model klasifikasi ML

---

# FR-17 ML Clustering Pola Belajar

Deskripsi

Sistem dapat mengelompokkan siswa berdasarkan pola akademik.

Fungsi

- Membagi siswa ke dalam cluster berdasarkan pola belajar
- Menampilkan profil setiap cluster
- Menampilkan anggota setiap kelompok

Target

Post-MVP: K-Means clustering

---

# FR-18 ML Model Management

Deskripsi

Administrator dapat mengelola model ML yang digunakan sistem.

Fungsi

- Melihat daftar model dan metriknya
- Melatih (training) model baru
- Mengaktifkan/nonaktifkan model
- Melihat riwayat prediksi

---

# Ringkasan Functional Requirements

| Kode  | Functional Requirement           | Prioritas |
|-------|----------------------------------|-----------|
| FR-01 | Authentication                   | P0        |
| FR-02 | Academic Year Management         | P1        |
| FR-03 | Teacher Assignment               | P1        |
| FR-04 | Student Management               | P1        |
| FR-05 | Student Academic Record          | P2        |
| FR-06 | Longitudinal Student Profile     | P2        |
| FR-07 | Student Timeline                 | P2        |
| FR-08 | Administrative Preparation Workspace | P4     |
| FR-09 | Administrative Validation        | P4        |
| FR-10 | Preview Buku Induk               | P4        |
| FR-11 | AI Student Summary               | P3        |
| FR-12 | AI Draft Deskripsi               | P3        |
| FR-13 | AI Student Transition Summary    | P3        |
| FR-14 | Dashboard                        | P4        |
| FR-15 | ML Trend Prediction              | P5        |
| FR-16 | ML Early Warning System          | P5        |
| FR-17 | ML Clustering Pola Belajar       | P5        |
| FR-18 | ML Model Management              | P5        |

Prioritas: P0 (Critical) → P5 (Post-MVP)

---

---

# Non-Functional Requirements

## NFR-01 Keamanan

| Kode    | Requirement                                       | Prioritas |
|---------|---------------------------------------------------|-----------|
| NFR-01  | Password harus di-hash menggunakan bcrypt/argon2  | High      |
| NFR-02  | Semua request ke backend harus menggunakan JWT    | High      |
| NFR-03  | Token JWT harus memiliki expiry time              | High      |
| NFR-04  | Endpoint AI harus memiliki rate limiting          | Medium    |
| NFR-05  | Input dari user harus divalidasi sebelum diproses | High      |

## NFR-02 Performa

| Kode    | Requirement                                           | Prioritas |
|---------|-------------------------------------------------------|-----------|
| NFR-06  | Response API untuk operasi CRUD < 500ms               | Medium    |
| NFR-07  | Response AI summary < 10 detik (termasuk LLM call)    | Medium    |
| NFR-08  | Halaman dashboard load < 3 detik                      | Medium    |
| NFR-09  | Database query untuk profile siswa < 1 detik          | Medium    |

## NFR-03 Usability

| Kode    | Requirement                                           | Prioritas |
|---------|-------------------------------------------------------|-----------|
| NFR-10  | Antarmuka harus responsif (desktop & tablet)          | High      |
| NFR-11  | Seluruh aksi harus memberikan feedback visual         | High      |
| NFR-12  | Proses loading AI harus menampilkan indikator progress| Medium    |
| NFR-13  | Form input harus memiliki validasi client-side        | Medium    |

## NFR-04 Reliability

| Kode    | Requirement                                           | Prioritas |
|---------|-------------------------------------------------------|-----------|
| NFR-14  | Sistem harus tetap berjalan jika LLM API gagal        | High      |
| NFR-15  | Error LLM API harus ditampilkan dengan pesan jelas    | High      |
| NFR-16  | Data yang sudah disimpan tidak boleh hilang           | High      |
| NFR-17  | Database harus memiliki backup                        | Medium    |

## NFR-05 Maintainability

| Kode    | Requirement                                           | Prioritas |
|---------|-------------------------------------------------------|-----------|
| NFR-18  | Kode harus mengikuti struktur modular (controller/service) | High    |
| NFR-19  | Environment variable harus dipisahkan dari kode       | High      |
| NFR-20  | API response harus memiliki format seragam            | High      |
| NFR-21  | Setiap modul harus memiliki validasi input            | Medium    |

## NFR-06 Compatibility

| Kode    | Requirement                                           | Prioritas |
|---------|-------------------------------------------------------|-----------|
| NFR-22  | Frontend harus kompatibel dengan Chrome, Firefox, Edge| High      |
| NFR-23  | Sistem harus berjalan di Bun runtime                  | High      |
| NFR-24  | Database harus kompatibel dengan MySQL/MariaDB        | High      |

---

# Ruang Lingkup MVP

## Fitur Utama (Termasuk MVP)

| Kode    | Fitur                               | Prioritas |
|---------|-------------------------------------|-----------|
| FR-01   | Authentication                      | P0        |
| FR-02   | Academic Year Management             | P1        |
| FR-03   | Teacher Assignment                   | P1        |
| FR-04   | Student Management                   | P1        |
| FR-05   | Student Academic Record              | P2        |
| FR-06   | Longitudinal Student Profile         | P2        |
| FR-07   | Student Timeline                     | P2        |
| FR-08   | Administrative Preparation Workspace | P4        |
| FR-09   | Administrative Validation            | P4        |
| FR-10   | Preview Buku Induk                   | P4        |
| FR-11   | AI Student Summary                   | P3        |
| FR-12   | AI Draft Deskripsi                   | P3        |
| FR-13   | AI Student Transition Summary        | P3        |
| FR-14   | Dashboard                            | P4        |

Prioritas: P0 (Critical) → P4 (Nice to have)

## Fitur Tidak Termasuk MVP

Fitur berikut tidak termasuk dalam MVP dan menjadi rencana pengembangan selanjutnya.

- Chatbot AI
- RAG (Retrieval-Augmented Generation)
- Vector Database
- Prediksi Prestasi Siswa
- Early Warning System
- Dashboard Orang Tua
- Notifikasi Otomatis