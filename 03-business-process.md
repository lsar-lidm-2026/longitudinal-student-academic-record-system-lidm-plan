# Business Process Analysis

## Longitudinal Student Academic Record Berbasis Artificial Intelligence untuk Mendukung Administrasi Akademik dan Penyusunan Buku Induk Siswa Sekolah Dasar

---

# Pendahuluan

Business Process merupakan tahapan yang menjelaskan bagaimana proses administrasi akademik berlangsung saat ini (As-Is Process), permasalahan yang muncul pada setiap tahapan (Pain Points), serta bagaimana sistem yang diusulkan akan memperbaiki proses tersebut (To-Be Process).

Tahapan ini sangat penting karena akan menjadi dasar bagi seluruh pengembangan sistem, mulai dari penyusunan Functional Requirement, Use Case Diagram, Activity Diagram, ERD, Database, hingga implementasi Artificial Intelligence.

Sistem yang dikembangkan **bukan bertujuan menggantikan Buku Induk Siswa**, melainkan membantu guru dalam mempersiapkan seluruh administrasi akademik sehingga proses penulisan Buku Induk yang masih wajib dilakukan secara manual dapat menjadi jauh lebih efisien.

---

# Tujuan Business Process

Business Process ini disusun untuk:

* Memahami alur administrasi akademik yang berjalan di sekolah dasar.
* Mengidentifikasi titik permasalahan pada setiap proses.
* Mendesain proses baru yang lebih efisien tanpa mengubah regulasi yang berlaku.
* Menentukan posisi Artificial Intelligence dalam proses administrasi.
* Menjadi acuan seluruh anggota tim dalam memahami sistem yang akan dibangun.

---

# AS-IS BUSINESS PROCESS

## (Kondisi Saat Ini)

Berdasarkan hasil observasi dan wawancara dengan guru sekolah dasar, alur administrasi akademik berlangsung sebagai berikut.

---

## Fase 1

## Penerimaan Peserta Didik Baru (SPMB)

### Aktivitas

Calon siswa melakukan pendaftaran.

↓

Mengisi formulir pendaftaran.

↓

Melampirkan dokumen.

* Akta Kelahiran
* Kartu Keluarga
* Dokumen pendukung lainnya

↓

Sekolah melakukan verifikasi.

↓

Operator Sekolah memasukkan data ke Dapodik.

↓

Siswa resmi terdaftar.

### Output

* Biodata siswa
* NIS
* NISN
* Data Orang Tua
* Data Administrasi Awal

### Catatan

Pada tahap ini Buku Induk Siswa belum dibuat.

---

## Fase 2

## Penentuan Wali Kelas

Pada awal tahun ajaran.

Kepala Sekolah menentukan pembagian kelas.

↓

Guru ditugaskan menjadi wali kelas.

↓

Guru menerima daftar siswa.

↓

Pembelajaran dimulai.

### Kondisi Saat Ini

Guru belum memiliki gambaran utuh mengenai perkembangan seluruh siswa yang akan menjadi tanggung jawabnya.

Informasi biasanya diperoleh melalui:

* komunikasi dengan wali kelas sebelumnya
* melihat rapor
* melihat arsip apabila diperlukan

Belum terdapat sistem yang mampu memberikan gambaran perkembangan siswa secara menyeluruh.

---

## Fase 3

## Proses Pembelajaran

Selama satu semester.

Guru melakukan:

* Absensi
* Penilaian Harian
* Tugas
* Ulangan
* PTS
* PAS
* Penilaian Sikap
* Catatan Perkembangan
* Prestasi
* Data Kesehatan

Semua data tersebut menjadi dasar penyusunan rapor.

---

## Fase 4

## Rekapitulasi Nilai

Menjelang akhir semester.

Guru mulai:

Mengumpulkan nilai.

↓

Menghitung nilai akhir.

↓

Melakukan rekap.

↓

Validasi.

↓

Finalisasi.

### Kondisi Saat Ini

Tahapan ini cukup menyita waktu karena guru harus memastikan seluruh nilai telah lengkap.

---

## Fase 5

## Penyusunan Rapor

Setelah seluruh nilai selesai.

Guru menyusun:

Nilai

↓

Konversi

↓

Deskripsi

↓

Rapor

### Kondisi Saat Ini

Bagian yang paling melelahkan menurut narasumber adalah menyusun deskripsi setiap mata pelajaran untuk seluruh siswa.

---

## Fase 6

## Penyusunan Buku Induk Siswa

Inilah proses yang menjadi fokus utama penelitian.

Guru membuka:

* Rapor
* Nilai
* Absensi
* Biodata
* Data kesehatan
* Prestasi

↓

Guru menyalin seluruh informasi tersebut secara manual ke Buku Induk.

↓

Dilakukan halaman demi halaman.

↓

Dilakukan untuk seluruh siswa.

### Catatan

Buku Induk wajib ditulis tangan.

Tidak terdapat sistem yang secara khusus membantu proses penyusunannya.

---

## Fase 7

## Pengarsipan

Setelah selesai.

Buku Induk disimpan sebagai arsip sekolah.

↓

Disimpan bertahun-tahun.

↓

Digunakan apabila sewaktu-waktu diperlukan.

---

## Fase 8

## Kenaikan Kelas

Pada akhir tahun.

Siswa naik kelas.

↓

Wali kelas berganti.

↓

Administrasi dimulai kembali.

↓

Guru baru mulai mengenal siswa.

---

# Identifikasi Pain Points

Berdasarkan hasil observasi dan wawancara, diperoleh beberapa permasalahan utama.

---

## Pain Point 1

### Data Akademik Tidak Terintegrasi

Data siswa tersebar pada berbagai dokumen.

Contohnya:

* Rapor
* Buku Induk
* Daftar Nilai
* Arsip
* Catatan Guru

Guru harus membuka banyak dokumen untuk memperoleh informasi lengkap mengenai seorang siswa.

---

## Pain Point 2

### Administrasi Berulang

Informasi yang sama ditulis berulang kali.

Misalnya.

Nilai

↓

Rapor

↓

Buku Induk

↓

Arsip

Walaupun tidak selalu identik, guru tetap harus melakukan proses penyalinan data berulang.

---

## Pain Point 3

### Monitoring Longitudinal Sulit

Guru mengetahui perkembangan siswa melalui pengalaman mengajar.

Namun sistem tidak menyediakan:

* riwayat akademik terintegrasi
* perkembangan setiap semester
* visualisasi perkembangan
* ringkasan historis siswa

Akibatnya monitoring sangat bergantung pada pengalaman guru.

---

## Pain Point 4

### Pergantian Wali Kelas

Setiap tahun wali kelas berubah.

Guru baru memerlukan waktu sekitar satu hingga dua bulan untuk memahami karakter dan kemampuan seluruh siswa.

Transfer informasi masih mengandalkan:

* komunikasi
* pengalaman guru sebelumnya
* dokumen administrasi

Belum terdapat sistem yang membantu proses transisi tersebut.

---

## Pain Point 5

### Penyusunan Buku Induk

Walaupun data sudah tersedia.

Guru tetap harus:

* membuka rapor
* membuka data nilai
* membuka absensi
* membuka biodata

Kemudian menulis ulang seluruh data secara manual.

Proses ini menjadi salah satu pekerjaan administrasi yang paling memakan waktu.

---

## Pain Point 6

### Validasi Data Manual

Guru harus memastikan sendiri.

Apakah:

* nilai sudah lengkap
* absensi sudah lengkap
* kesehatan sudah terisi
* identitas sesuai

Belum terdapat mekanisme otomatis untuk memeriksa kelengkapan data.

---

## Pain Point 7

### Pencarian Arsip Lama

Ketika membutuhkan data beberapa tahun sebelumnya.

Guru harus mencari:

* Buku Induk
* Arsip
* Dokumen lama

Proses ini bergantung pada ketersediaan dokumen dan penanggung jawab arsip.

---

# TO-BE BUSINESS PROCESS

## (Sistem yang Diusulkan)

Sistem yang diusulkan **tidak mengubah regulasi**.

Buku Induk tetap ditulis tangan.

Perubahan hanya terjadi pada proses persiapan administrasi.

---

## Fase 1

## Penerimaan Peserta Didik Baru

Operator menginput data siswa.

↓

Longitudinal Student Academic Record dibuat secara otomatis.

↓

Seluruh histori siswa akan disimpan dalam satu sistem.

---

## Fase 2

## Manajemen Tahun Ajaran

Operator membuat Tahun Ajaran baru.

↓

Membuka Semester.

↓

Melakukan kenaikan kelas.

↓

Mengarsipkan semester sebelumnya.

---

## Fase 3

## Penugasan Wali Kelas

Operator melakukan Teacher Assignment.

Contoh.

Tahun Ajaran 2027

↓

Bu Ani

↓

Kelas 5A

Hak akses guru berubah secara otomatis sesuai penugasan.

Guru hanya dapat mengakses siswa yang menjadi tanggung jawabnya.

Hal ini lebih fleksibel dibanding menjadikan "wali kelas" sebagai role permanen, karena penugasan dapat berubah setiap tahun.

---

## Fase 4

## Pengelolaan Akademik

Guru tetap melakukan aktivitas seperti biasa.

* Penilaian
* Kehadiran
* Prestasi
* Data kesehatan
* Catatan perkembangan

Perbedaannya.

Seluruh data langsung tersimpan pada Longitudinal Student Academic Record.

Tidak ada pembuatan histori baru setiap tahun.

Histori akan terus bertambah secara otomatis.

---

## Fase 5

## Longitudinal Student Academic Record

Setiap siswa memiliki satu halaman profil.

Berisi.

* Biodata
* Timeline kelas 1 hingga kelas 6
* Nilai setiap semester
* Kehadiran
* Prestasi
* Data kesehatan
* Riwayat perkembangan
* Deskripsi semester sebelumnya

Guru dapat melihat perkembangan akademik siswa secara menyeluruh tanpa harus membuka banyak dokumen.

---

## Fase 6

## Artificial Intelligence Assistance

Artificial Intelligence digunakan sebagai asisten guru.

Bukan sebagai pengambil keputusan.

AI membantu menghasilkan.

### AI Student Summary

Merangkum perkembangan siswa berdasarkan histori akademik.

---

### AI Draft Deskripsi

Menyusun draft deskripsi rapor berdasarkan:

* histori nilai
* histori deskripsi
* kehadiran
* prestasi

Guru tetap melakukan validasi dan revisi.

---

### AI Student Transition Summary

Ketika wali kelas berganti.

Sistem secara otomatis membuat ringkasan setiap siswa.

Saat wali kelas baru login.

Ringkasan seluruh siswa sudah tersedia.

Guru tidak perlu lagi mencari informasi dari banyak dokumen.

---

## Fase 7

## Administrative Preparation Workspace

Seluruh data yang diperlukan untuk penyusunan Buku Induk dikumpulkan dalam satu halaman.

Misalnya.

* Biodata
* Nilai
* Kehadiran
* Prestasi
* Kesehatan
* Deskripsi

Guru tidak perlu lagi membuka banyak dokumen.

---

## Fase 8

## Administrative Validation

Sistem melakukan pemeriksaan otomatis.

Contoh.

✓ Nilai lengkap

✓ Kehadiran lengkap

✓ Biodata lengkap

✗ Berat badan belum diisi

✗ Tinggi badan belum diisi

Guru hanya melengkapi data yang masih kosong.

---

## Fase 9

## Preview Buku Induk

Sistem menampilkan tampilan yang menyerupai isi Buku Induk.

Namun.

Sistem tidak menghasilkan Buku Induk Digital.

Guru tetap menulis Buku Induk secara manual sesuai regulasi.

Preview hanya berfungsi sebagai media persiapan agar proses penulisan menjadi lebih cepat dan mengurangi risiko kesalahan.

---

## Fase 10

## Pergantian Wali Kelas

Pada awal tahun ajaran.

Operator.

↓

Melakukan kenaikan kelas.

↓

Melakukan Teacher Assignment.

↓

Hak akses guru berubah.

↓

AI menghasilkan Student Transition Summary.

↓

Guru baru login.

↓

Melihat seluruh siswa beserta ringkasan perkembangan masing-masing.

↓

Pembelajaran dimulai.

---

# Perbandingan As-Is dan To-Be

| As-Is                                                             | To-Be                                                                          |
| ----------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| Data tersebar di berbagai dokumen                                 | Seluruh data akademik terpusat dalam Longitudinal Student Academic Record      |
| Guru membuka banyak dokumen                                       | Guru melihat satu profil siswa yang lengkap                                    |
| Monitoring perkembangan bergantung pengalaman guru                | Riwayat perkembangan tersedia dalam timeline longitudinal                      |
| Pergantian wali kelas mengandalkan komunikasi                     | AI menghasilkan Student Transition Summary                                     |
| Penyusunan Buku Induk dilakukan dengan membuka banyak sumber data | Administrative Preparation Workspace mengumpulkan seluruh data yang diperlukan |
| Validasi kelengkapan data dilakukan manual                        | Administrative Validation membantu memeriksa kelengkapan data                  |
| Buku Induk tetap ditulis tangan                                   | Sistem hanya membantu persiapan administrasi dan preview sesuai regulasi       |

---

# Prinsip Desain Sistem

Seluruh pengembangan sistem harus mengikuti prinsip berikut.

1. Tidak menggantikan Buku Induk fisik.

2. Tidak mengubah regulasi administrasi sekolah.

3. Mengurangi pekerjaan administrasi yang berulang.

4. Menyediakan riwayat akademik siswa secara berkelanjutan sejak kelas 1 hingga kelas 6.

5. Membantu transisi wali kelas melalui penyediaan informasi yang terstruktur.

6. Memanfaatkan Artificial Intelligence sebagai pendukung keputusan guru, bukan sebagai pengganti guru.

7. Menjadikan Longitudinal Student Academic Record sebagai sumber data utama (single source of truth) untuk seluruh riwayat akademik siswa.

---

# Catatan Desain yang Masih Perlu Diputuskan

Masih terdapat satu keputusan arsitektur penting yang akan memengaruhi pengembangan sistem.

Yaitu mengenai sumber data akademik.

Alternatif yang dapat dipilih adalah:

1. Guru menginput seluruh data langsung ke sistem.

Kelebihan:

* Implementasi lebih sederhana.
* Data langsung tersimpan.

Kekurangan:

* Berpotensi menambah beban administrasi guru.

2. Sistem menyediakan mekanisme impor data dari aplikasi rapor atau format Excel.

Kelebihan:

* Guru tidak perlu mengetik ulang.
* Lebih realistis diterapkan sebagai MVP.
* Tidak mengubah kebiasaan kerja guru secara signifikan.

Kekurangan:

* Memerlukan perancangan format impor yang konsisten.

3. Sistem terintegrasi langsung dengan Dapodik atau aplikasi rapor yang sudah digunakan sekolah.

Kelebihan:

* Data tersinkronisasi secara otomatis.

Kekurangan:

* Kompleksitas implementasi jauh lebih tinggi.
* Kurang realistis untuk ruang lingkup MVP LIDM.

Berdasarkan ruang lingkup kompetisi dan keterbatasan waktu pengembangan, alternatif kedua dinilai sebagai pilihan yang paling realistis untuk MVP karena mampu mengurangi pekerjaan berulang tanpa mengubah alur administrasi yang sudah berjalan di sekolah.
