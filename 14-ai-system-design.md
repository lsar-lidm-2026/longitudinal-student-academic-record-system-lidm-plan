# AI System Design
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

# Pendahuluan

Artificial Intelligence pada sistem ini berfungsi sebagai asisten bagi guru dalam mengolah data akademik siswa menjadi informasi yang lebih mudah dipahami.

AI tidak menggantikan peran guru dalam melakukan penilaian maupun pengambilan keputusan.

Seluruh hasil yang dihasilkan AI tetap dapat diperiksa, diubah, maupun diabaikan oleh guru sebelum digunakan.

Dengan demikian, Artificial Intelligence berperan sebagai decision support system, bukan decision maker.

---

# Tujuan AI

Artificial Intelligence dikembangkan untuk membantu guru dalam:

- memahami perkembangan akademik siswa
- menyusun deskripsi rapor
- mempermudah proses pergantian wali kelas
- mempercepat penyusunan administrasi Buku Induk

---

# Arsitektur AI

```text
                 Guru
                  │
                  ▼
      Longitudinal Academic Record
                  │
                  ▼
        AI Processing Engine
                  │
        ┌─────────┼──────────┐
        │         │          │
        ▼         ▼          ▼
 Student     Draft Report   Transition
 Summary     Description     Summary
                  │
                  ▼
          Review oleh Guru
                  │
                  ▼
        Digunakan dalam Sistem
```

---

# Komponen AI

## 1. Input Data

Artificial Intelligence menggunakan data yang telah tersedia pada database sistem.

Data tersebut meliputi:

- Biodata siswa
- Nilai setiap mata pelajaran
- Nilai rata-rata
- Kehadiran
- Prestasi
- Data kesehatan
- Catatan guru

AI tidak menerima input secara manual selain permintaan dari guru.

---

## 2. AI Processing

Setelah guru memilih siswa, sistem akan mengumpulkan seluruh data akademik yang berkaitan.

Data tersebut kemudian disusun menjadi prompt terstruktur sebelum dikirim ke Large Language Model (LLM).

LLM menghasilkan narasi berdasarkan data yang diberikan tanpa melakukan perubahan terhadap data asli.

---

## 3. Output AI

Artificial Intelligence menghasilkan tiga fitur utama.

### AI Student Summary

Merangkum perkembangan akademik siswa secara singkat.

Contoh informasi:

- mata pelajaran yang paling menonjol
- peningkatan prestasi
- kecenderungan kehadiran
- prestasi siswa
- catatan perkembangan

---

### AI Draft Deskripsi

Menghasilkan draft deskripsi rapor berdasarkan data akademik siswa.

Guru tetap dapat mengubah hasil yang diberikan AI sebelum digunakan.

---

### AI Student Transition Summary

Menghasilkan ringkasan perkembangan siswa untuk membantu wali kelas berikutnya memahami kondisi siswa secara lebih cepat.

Ringkasan ini memuat informasi penting seperti:

- perkembangan akademik
- kekuatan siswa
- area yang masih perlu ditingkatkan
- catatan penting guru sebelumnya

---

# AI Workflow

```text
Guru

↓

Memilih Siswa

↓

Sistem Mengambil Data Akademik

↓

Menyusun Prompt

↓

Large Language Model

↓

Generate Narasi

↓

Guru Review

↓

Guru Edit (Opsional)

↓

Simpan
```

---

# Human in the Loop

Setiap keluaran Artificial Intelligence harus melalui proses validasi guru.

Guru memiliki hak penuh untuk:

- menerima hasil AI
- mengubah hasil AI
- menghapus hasil AI
- membuat ulang hasil AI

Dengan demikian seluruh keputusan akademik tetap berada pada guru.

---

# Kelebihan Pendekatan

Pendekatan ini memiliki beberapa keunggulan.

- AI tidak mengubah data akademik.
- AI hanya menghasilkan narasi.
- Guru tetap menjadi pengambil keputusan.
- Hasil AI mudah diperiksa kembali.
- Risiko kesalahan dapat diminimalkan.

---

# Batasan Sistem

Pada MVP ini Artificial Intelligence belum melakukan:

- prediksi prestasi siswa
- deteksi dini siswa berisiko
- chatbot
- pencarian dokumen menggunakan RAG
- analisis dokumen PDF
- OCR Buku Induk
- analisis gambar

Seluruh fitur tersebut merupakan rencana pengembangan pada tahap berikutnya.

---

# Roadmap Pengembangan AI

Tahap 1 (MVP)

- AI Student Summary
- AI Draft Deskripsi
- AI Student Transition Summary

Tahap 2

- Chatbot AI
- Retrieval-Augmented Generation (RAG)
- Vector Database

Tahap 3

- Prediksi Prestasi Siswa
- Early Warning System
- Dashboard Orang Tua
- Notifikasi Otomatis

---

# Kesimpulan

Artificial Intelligence pada sistem ini dirancang sebagai pendamping guru dalam mengolah riwayat akademik siswa menjadi informasi yang lebih ringkas, mudah dipahami, dan siap digunakan dalam proses administrasi.

Pendekatan ini dipilih agar sistem tetap sederhana, mudah dikembangkan, dan sesuai dengan ruang lingkup Minimum Viable Product (MVP) pada Lomba Inovasi Digital Mahasiswa (LIDM), tanpa mengubah mekanisme administrasi sekolah yang tetap mengikuti regulasi yang berlaku.