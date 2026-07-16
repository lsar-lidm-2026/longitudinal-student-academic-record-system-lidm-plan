# Sequence Diagram

## SD-01 Login

User

↓

System

↓

Database

User → System : Login

System → Database : Validasi User

Database → System : Data User

System → User : Dashboard

---

## SD-02 Input Data Akademik

Wali Kelas

↓

System

↓

Database

Wali Kelas → System : Input Data

System → Database : Simpan Data

Database → System : Success

System → Wali Kelas : Data Tersimpan

---

## SD-03 Generate AI Student Summary

Wali Kelas

↓

System

↓

Database

↓

AI Service

Wali Kelas → System : Pilih Siswa

System → Database : Ambil Riwayat

Database → System : Riwayat Akademik

System → AI Service : Generate Summary

AI Service → System : Hasil Ringkasan

System → Wali Kelas : Tampilkan Ringkasan

---

## SD-04 Generate Draft Deskripsi

Wali Kelas

↓

System

↓

Database

↓

AI Service

Wali Kelas → System : Generate Draft

System → Database : Ambil Nilai

Database → System : Nilai

System → AI Service : Generate Draft

AI Service → System : Draft Deskripsi

System → Wali Kelas : Tampilkan Draft

---

## SD-05 Generate Student Transition Summary

Wali Kelas

↓

System

↓

Database

↓

AI Service

Wali Kelas → System : Generate Transition Summary

System → Database : Ambil Riwayat

Database → System : Riwayat Akademik

System → AI Service : Generate Summary

AI Service → System : Transition Summary

System → Wali Kelas : Tampilkan Hasil

---

## SD-06 Preview Buku Induk

Wali Kelas

↓

System

↓

Database

Wali Kelas → System : Preview Buku Induk

System → Database : Ambil Seluruh Data

Database → System : Data Lengkap

System → Wali Kelas : Preview Siap Ditulis Manual