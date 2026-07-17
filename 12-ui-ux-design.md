# UI/UX Design
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Design Principle

Antarmuka sistem dirancang berdasarkan hasil wawancara dengan guru sekolah dasar.

Prinsip utama desain adalah:

- sederhana
- mudah dipelajari
- meminimalkan klik
- fokus pada pekerjaan wali kelas
- AI sebagai asisten, bukan pengganti guru

Desain menggunakan pendekatan dashboard modern dengan navigasi sederhana sehingga guru dapat menyelesaikan pekerjaan administrasi lebih cepat.

---

# Sitemap

```

Login

↓

Dashboard

├── Data Siswa

├── Riwayat Akademik

├── Input Semester

├── AI Assistant

├── Preview Buku Induk

└── Pengaturan

```

---

# Halaman 1
## Login

Tujuan

Autentikasi pengguna.

Komponen

- Logo Sistem
- Email
- Password
- Tombol Login
- Forgot Password (Opsional)

---

# Halaman 2
## Dashboard

Tujuan

Menampilkan ringkasan pekerjaan wali kelas.

Widget

- Jumlah siswa
- Jumlah kelas
- Tahun ajaran aktif
- Progress pengisian semester
- Jumlah draft AI yang belum direview

Shortcut

- Input Nilai
- AI Summary
- Preview Buku Induk

---

Contoh Layout

```

----------------------------------------------------
Logo

Dashboard Wali Kelas

----------------------------------------------------

32 Siswa

Semester Genap

Progress 85%

Draft AI 5

----------------------------------------------------

[ Input Semester ]

[ Riwayat Siswa ]

[ AI Assistant ]

[ Buku Induk ]

----------------------------------------------------

```

---

# Halaman 3

## Data Siswa

Tujuan

Melihat seluruh siswa pada kelas yang diampu.

Komponen

- Search
- Filter
- Daftar siswa

Kolom

- Nama
- NISN
- Kelas
- Status

Aksi

- Lihat Detail

---

Contoh

```

Cari siswa...

---------------------------------------------------------

Nama

NISN

Kelas

Aksi

---------------------------------------------------------

Andi

001

5A

Detail

Budi

002

5A

Detail

Citra

003

5A

Detail

---------------------------------------------------------

```

---

# Halaman 4

## Detail Siswa

Tujuan

Menampilkan seluruh informasi siswa.

Bagian

### Biodata

- Nama
- NIS
- NISN
- Orang tua
- Alamat

### Ringkasan Akademik

- Nilai rata-rata
- Kehadiran
- Prestasi

### Timeline Semester

Semester 1

↓

Semester 2

↓

Semester 3

↓

Semester 4

↓

Semester 5

↓

Semester 6

↓

Semester berjalan

Guru cukup memilih semester untuk melihat detail.

---

# Halaman 5

## Input Semester

Tujuan

Menginput data akademik siswa.

Tab

### Nilai

- Mata pelajaran
- Nilai

### Kehadiran

- Sakit
- Izin
- Alpha

### Prestasi

Tambah prestasi

### Kesehatan

- Tinggi badan
- Berat badan
- Catatan

### Catatan Guru

Text Area

Tombol

Simpan

---

# Halaman 6

## AI Assistant

Tujuan

Membantu guru membuat narasi.

Fitur

### Student Summary

Ringkasan perkembangan siswa.

Tombol

Generate

---

### Draft Deskripsi

Draft deskripsi rapor.

Tombol

Generate

---

### Student Transition Summary

Ringkasan untuk wali kelas berikutnya.

Tombol

Generate

---

Layout

```

------------------------------------------------

Student Summary

[ Generate ]

------------------------------------------------

Draft Deskripsi

[ Generate ]

------------------------------------------------

Transition Summary

[ Generate ]

------------------------------------------------

Output AI

-----------------------------------------------

...

-----------------------------------------------

[ Edit ]

[ Simpan ]

------------------------------------------------

```

---

# Halaman 7

## Preview Buku Induk

Tujuan

Membantu guru menyalin data ke Buku Induk manual.

Halaman ini bukan untuk mencetak Buku Induk.

Sistem hanya menyusun seluruh data sesuai urutan administrasi.

Bagian

- Biodata
- Nilai
- Kehadiran
- Prestasi
- Kesehatan
- Catatan

Guru tinggal melihat layar lalu menyalin ke Buku Induk.

---

Layout

```

===================================

BIODATA

===================================

Nama

NIS

NISN

...

===================================

NILAI

===================================

Bahasa Indonesia

Matematika

IPA

...

===================================

KEHADIRAN

===================================

Sakit

Izin

Alpha

===================================

```

---

# Halaman 8

## Pengaturan

Menu

- Profil
- Password
- Tahun Ajaran Aktif
- Logout

---

# Hak Akses

## Administrator

- Kelola pengguna
- Kelola kelas
- Kelola tahun ajaran

---

## Operator

- Kelola data siswa

---

## Wali Kelas

- Dashboard
- Data siswa
- Input semester
- AI Assistant
- Preview Buku Induk

---

## Kepala Sekolah

- Dashboard
- Data siswa
- Riwayat akademik
- Preview Buku Induk

---

# Alur Penggunaan

Login

↓

Dashboard

↓

Pilih Kelas

↓

Pilih Siswa

↓

Input Semester

↓

Generate AI

↓

Review AI

↓

Simpan

↓

Preview Buku Induk

↓

Guru menyalin ke Buku Induk manual

---

# Desain Visual

Konsep visual menggunakan gaya modern dan minimalis.

Warna utama

- Biru sebagai warna utama
- Putih sebagai latar belakang
- Abu-abu muda untuk kartu informasi
- Hijau untuk indikator keberhasilan

Komponen

- Card
- Table
- Timeline
- Progress Bar
- Button
- Modal
- Form Input

---

---

# Color Palette & Design Tokens

| Token              | Value      | Penggunaan                          |
|--------------------|------------|--------------------------------------|
| Primary            | #2563EB    | Tombol, link, header aktif           |
| Primary Hover      | #1D4ED8    | Tombol hover                         |
| Primary Light      | #DBEAFE    | Background selected, badges          |
| Success            | #16A34A    | Status lengkap, sukses               |
| Warning            | #D97706    | Peringatan, draft AI belum review    |
| Danger             | #DC2626    | Error, data belum lengkap            |
| Neutral 50         | #F8FAFC    | Background halaman                   |
| Neutral 100        | #F1F5F9    | Card, sidebar                        |
| Neutral 200        | #E2E8F0    | Border, divider                      |
| Neutral 700        | #334155    | Teks body                            |
| Neutral 900        | #0F172A    | Teks heading                         |

# Typography

| Level     | Font Size | Weight  | Usage                     |
|-----------|-----------|---------|---------------------------|
| h1        | 24px      | Bold    | Judul halaman             |
| h2        | 18px      | Semibold| Judul section             |
| h3        | 16px      | Semibold| Judul card                |
| body      | 14px      | Normal  | Teks konten               |
| small     | 12px      | Normal  | Label, helper text        |

# Component Types (MVP)

| Component         | States                        | Usage                                |
|-------------------|-------------------------------|--------------------------------------|
| Button            | default, hover, disabled, loading | Aksi utama                       |
| Input Text        | default, focus, error, disabled | Form input                        |
| Select            | default, focus, error          | Dropdown pilihan                   |
| Table             | default, hover, empty          | Daftar siswa, nilai                |
| Card              | default                        | Widget dashboard, ringkasan        |
| Badge             | info, success, warning, danger | Status kelengkapan, role           |
| Modal             | open, close                    | Konfirmasi, detail                 |
| Timeline          | vertical, horizontal           | Riwayat semester siswa             |
| Progress Bar      | 0-100%                         | Progress pengisian semester        |
| Tabs              | active, inactive               | Input semester (nilai/kehadiran/dll)|
| Loading Spinner   | active                         | Loading state AI, fetch data       |
| Empty State       | illustration + text            | Belum ada data                     |
| Error State       | icon + message + retry         | Error API / AI                     |

# Layout Structure

```
+--------------------------------------------------+
|  Sidebar          |  Top Navigation              |
|  ┌─────────┐      |  [Search] [Profile] [Logout] |
|  │ Logo    │      +-------------------------------+
|  ├─────────┤                                     |
|  │ Dashboard│     |  Main Content Area           |
|  │ Students │     |  (Halaman aktif)             |
|  │ Classes  │     |                               |
|  │ AI       │     |                               |
|  │ Buku     │     |                               |
|  │ Induk    │     |                               |
|  ├─────────┤      |                               |
|  │ Settings │     |                               |
|  └─────────┘      |                               |
+--------------------------------------------------+
```

Hak akses menentukan menu yang muncul di sidebar:
- **Guru**: Dashboard, Students, AI Assistant, Buku Induk
- **Admin**: Dashboard, Users, Classes, Academic Years
- **Operator**: Dashboard, Students
- **Kepala Sekolah**: Dashboard, Students, Buku Induk (read-only)

# Responsive Design

| Breakpoint | Width    | Layout                    |
|------------|----------|---------------------------|
| Mobile     | < 768px  | Single column, hamburger menu |
| Tablet     | 768-1024px| Sidebar collapsed, 2 column |
| Desktop    | > 1024px | Sidebar expanded, multi column |

Target utama: Desktop (karena guru bekerja menggunakan laptop/PC).

---

# Prinsip UI/UX

- Fokus pada tugas utama guru.
- Mengurangi jumlah klik (maksimal 3 klik untuk mencapai fitur utama).
- Informasi penting ditampilkan dalam satu halaman.
- AI selalu membutuhkan persetujuan guru sebelum digunakan.
- Tidak mengubah proses administrasi sekolah, tetapi membantu mempercepat pekerjaan guru.
- Antarmuka dibuat sederhana agar mudah dipahami oleh guru dengan berbagai tingkat literasi digital.
- Seluruh aksi memberikan feedback visual (loading, success, error).
- Data yang belum diisi ditandai jelas agar guru tahu apa yang harus dilengkapi.

# 