# Activity Diagram
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

Pendahuluan: Activity Diagram berikut menggambarkan alur kerja sistem menggunakan pendekatan swimlane. Setiap diagram memisahkan tanggung jawab aktor (User) dan sistem (System) dalam setiap proses.

---

## Activity 1: Login

```
┌─────────────────────────────────────┬──────────────────────────────────┐
│             User                    │            System                │
├─────────────────────────────────────┼──────────────────────────────────┤
│                                     │                                  │
│   Start                             │                                  │
│     │                               │                                  │
│     ▼                               │                                  │
│   Input username & password         │                                  │
│     │                               │                                  │
│     └───────────────────────────────→  Validasi akun                   │
│                                       │                                │
│                                  ┌────┴────┐                           │
│                                  │  Valid  │                           │
│                                  └────┬────┘                           │
│                                       │                                │
│                                       ▼                               │
│                                     Generate JWT token                 │
│                                       │                                │
│                                       ▼                               │
│   ←───────────────────────────────  Dashboard                         │
│     │                               │                                  │
│     ▼                               │                                  │
│   Akses fitur sesuai role            │                                  │
│     │                               │                                  │
│     ▼                               │                                  │
│   End                               │                                  │
│                                     │                                  │
│   [Invalid]                         │                                  │
│     │                               │                                  │
│     ←────────────────────────────── Tampilkan pesan error             │
│     │                               │                                  │
│     ▼                               │                                  │
│   Kembali ke form login             │                                  │
│                                     │                                  │
└─────────────────────────────────────┴──────────────────────────────────┘
```

---

## Activity 2: Input Data Akademik

```
┌─────────────────────────────────────┬──────────────────────────────────┬─────────────────────┐
│             Guru                    │            System                │     Database        │
├─────────────────────────────────────┼──────────────────────────────────┼─────────────────────┤
│                                     │                                  │                     │
│   Start                             │                                  │                     │
│     │                               │                                  │                     │
│     ▼                               │                                  │                     │
│   Pilih kelas                       │                                  │                     │
│     │                               │                                  │                     │
│     ▼                               │                                  │                     │
│   Pilih siswa                       │                                  │                     │
│     │                               │                                  │                     │
│     ▼                               │                                  │                     │
│   Pilih semester                    │                                  │                     │
│     │                               │                                  │                     │
│     ▼                               │                                  │                     │
│   Input data akademik:              │                                  │                     │
│   • Nilai (pengetahuan & skill)     │                                  │                     │
│   • Kehadiran (sakit/izin/alpha)    │                                  │                     │
│   • Prestasi                        │                                  │                     │
│   • Data kesehatan                  │                                  │                     │
│   • Catatan                         │                                  │                     │
│     │                               │                                  │                     │
│     └───────────────────────────────→  Validasi data                   │                     │
│                                       │                                │                     │
│                                       ▼                               │                     │
│                                     Cek hak akses                      │                     │
│                                       │                                │                     │
│                                  ┌────┴────┐                           │                     │
│                                  │  Valid  │                           │                     │
│                                  └────┬────┘                           │                     │
│                                       │                                │                     │
│                                       ▼                               │                     │
│                                     Simpan data ───────────────────────→ INSERT / UPSERT    │
│                                       │                                │                     │
│                                       │◄─────────────────────────────── Success             │
│                                       │                                │                     │
│   ←───────────────────────────────  Data tersimpan                    │                     │
│     │                               │                                  │                     │
│     ▼                               │                                  │                     │
│   End                               │                                  │                     │
│                                     │                                  │                     │
│   [Tidak memiliki akses]            │                                  │                     │
│     │                               │                                  │                     │
│     ←────────────────────────────── Tampilkan error 403               │                     │
│                                     │                                  │                     │
└─────────────────────────────────────┴──────────────────────────────────┴─────────────────────┘
```

---

## Activity 3: Generate AI Student Summary

```
┌─────────────────────────────┬─────────────────────────────────┬──────────────────┬────────────┐
│           Guru              │           System                │    Database      │  AI/LLM   │
├─────────────────────────────┼─────────────────────────────────┼──────────────────┼────────────┤
│                             │                                 │                  │            │
│  Start                      │                                 │                  │            │
│    │                        │                                 │                  │            │
│    ▼                        │                                 │                  │            │
│  Pilih siswa                │                                 │                  │            │
│    │                        │                                 │                  │            │
│    └────────────────────────→  Terima request                 │                  │            │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Ambil riwayat akademik ─────────────→ SELECT data     │            │
│                              │                                 │                  │            │
│                              │◄──────────────────────────────── Riwayat lengkap  │            │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Susun prompt terstruktur           │                  │            │
│                              │                                 │                  │            │
│                              └───────────────────────────────────────────────────→ Generate  │
│                              │                                 │                  │            │
│                              │◄─────────────────────────────────────────────────── Narasi    │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Simpan sebagai draft ───────────────→ INSERT          │            │
│                              │   (AiSummary, isFinal=false)    │  AiSummary      │            │
│                              │                                 │                  │            │
│  ←────────────────────────── Tampilkan hasil                   │                  │            │
│    │                        │                                 │                  │            │
│    ▼                        │                                 │                  │            │
│  Review hasil AI            │                                 │                  │            │
│    │                        │                                 │                  │            │
│    ├── [Terima] ────────────→  Update isFinal=true ───────────→ UPDATE           │            │
│    │                        │                                 │                  │            │
│    ├── [Edit] ──────────────→  Simpan hasil edit ─────────────→ UPDATE           │            │
│    │                        │                                 │                  │            │
│    └── [Regenerate] ────────→  Ulangi dari ambil riwayat       │                  │            │
│                              │   (version++) ─────────────────→ INSERT (v2)      │            │
│                              │                                 │                  │            │
│  End                        │                                 │                  │            │
│                             │                                 │                  │            │
└─────────────────────────────┴─────────────────────────────────┴──────────────────┴────────────┘
```

---

## Activity 4: Generate AI Draft Deskripsi Rapor

```
┌─────────────────────────────┬─────────────────────────────────┬──────────────────┬────────────┐
│           Guru              │           System                │    Database      │  AI/LLM   │
├─────────────────────────────┼─────────────────────────────────┼──────────────────┼────────────┤
│                             │                                 │                  │            │
│  Start                      │                                 │                  │            │
│    │                        │                                 │                  │            │
│    ▼                        │                                 │                  │            │
│  Pilih siswa & semester     │                                 │                  │            │
│    │                        │                                 │                  │            │
│    └────────────────────────→  Ambil nilai & catatan          │                  │            │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Ambil SubjectScore ─────────────────→ SELECT          │            │
│                              │                                 │                  │            │
│                            Ambil Catatan Guru                 │                  │            │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Ambil deskripsi sebelumnya ─────────→ SELECT          │            │
│                              │   (AiSummary DRAFT_DESCRIPTION) │                  │            │
│                              │                                 │                  │            │
│                              └───────────────────────────────────────────────────→ Generate  │
│                              │                                 │                  │  draft    │
│                              │◄─────────────────────────────────────────────────── Draft     │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Simpan draft ──────────────────────→ INSERT          │            │
│                              │   (summaryType=DRAFT_DESCRIPTION)│  AiSummary      │            │
│                              │                                 │                  │            │
│  ←────────────────────────── Tampilkan draft                   │                  │            │
│    │                        │                                 │                  │            │
│    ▼                        │                                 │                  │            │
│  Edit draft                 │                                 │                  │            │
│    │                        │                                 │                  │            │
│    └────────────────────────→  Simpan ─────────────────────────→ UPDATE          │            │
│                              │   (isFinal=true)                │                  │            │
│                              │                                 │                  │            │
│  End                        │                                 │                  │            │
│                             │                                 │                  │            │
└─────────────────────────────┴─────────────────────────────────┴──────────────────┴────────────┘
```

---

## Activity 5: Generate AI Student Transition Summary

```
┌─────────────────────────────┬─────────────────────────────────┬──────────────────┬────────────┐
│           Guru              │           System                │    Database      │  AI/LLM   │
├─────────────────────────────┼─────────────────────────────────┼──────────────────┼────────────┤
│                             │                                 │                  │            │
│  Start                      │                                 │                  │            │
│    │                        │                                 │                  │            │
│    ▼                        │                                 │                  │            │
│  Pilih kelas (seluruh siswa)│                                 │                  │            │
│    │                        │                                 │                  │            │
│    └────────────────────────→  Ambil seluruh SemesterRecord   │                  │            │
│                              │   milik setiap siswa           │                  │            │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Join data akademik ─────────────────→ SELECT          │            │
│                              │  (nilai, prestasi, kehadiran,   │  multi-table    │            │
│                              │   catatan dari semua semester)  │                  │            │
│                              │                                 │                  │            │
│                              └───────────────────────────────────────────────────→ Generate  │
│                              │                                 │                  │  summary  │
│                              │◄─────────────────────────────────────────────────── Ringkasan │
│                              │                                 │                  │            │
│                              ▼                                │                  │            │
│                            Simpan per siswa ───────────────────→ INSERT per      │            │
│                              │  (summaryType=TRANSITION_SUMMARY)|  AiSummary      │            │
│                              │                                 │                  │            │
│  ←────────────────────────── Tampilkan ringkasan               │                  │            │
│    │                        │                                 │                  │            │
│    ▼                        │                                 │                  │            │
│  Review & simpan            │                                 │                  │            │
│    │                        │                                 │                  │            │
│    ▼                        │                                 │                  │            │
│  End                        │                                 │                  │            │
└─────────────────────────────┴─────────────────────────────────┴──────────────────┴────────────┘
```

---

## Activity 6: Preview Buku Induk

```
┌─────────────────────────────┬─────────────────────────────────┬──────────────────┐
│           Guru              │           System                │    Database      │
├─────────────────────────────┼─────────────────────────────────┼──────────────────┤
│                             │                                 │                  │
│  Start                      │                                 │                  │
│    │                        │                                 │                  │
│    ▼                        │                                 │                  │
│  Pilih siswa                │                                 │                  │
│    │                        │                                 │                  │
│    └────────────────────────→  Ambil seluruh data siswa       │                  │
│                              │                                 │                  │
│                              ▼                                │                  │
│                            Ambil biodata ─────────────────────→ SELECT Student  │
│                              │                                 │                  │
│                            Ambil seluruh SemesterRecord       │                  │
│                              │  ──────────────────────────────→ SELECT           │
│                              │                                 │  SemesterRecord │
│                              ▼                                │   + join ke     │
│                            Ambil nilai setiap semester         │  SubjectScore,  │
│                              │  ──────────────────────────────→  Attendance,    │
│                              │                                 │  Achievement,   │
│                            Ambil kehadiran                     │  HealthRecord   │
│                              │  ──────────────────────────────→                  │
│                            Ambil prestasi                    │                  │
│                              │  ──────────────────────────────→                  │
│                            Ambil data kesehatan              │                  │
│                              │  ──────────────────────────────→                  │
│                              │                                 │                  │
│                              ▼                                │                  │
│                            Susun format Buku Induk             │                  │
│                              │                                 │                  │
│  ←────────────────────────── Tampilkan preview                 │                  │
│    │                        │                                 │                  │
│    ▼                        │                                 │                  │
│  Menyalin ke Buku Induk     │                                 │                  │
│  manual                     │                                 │                  │
│    │                        │                                 │                  │
│    ▼                        │                                 │                  │
│  End                        │                                 │                  │
└─────────────────────────────┴─────────────────────────────────┴──────────────────┘
```

---

## Activity 7: Teacher Assignment

```
┌─────────────────────────────────────┬──────────────────────────────────┬──────────────────────┐
│         Administrator               │            System                │      Database        │
├─────────────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│                                     │                                  │                      │
│   Start                             │                                  │                      │
│     │                               │                                  │                      │
│     ▼                               │                                  │                      │
│   Pilih kelas                       │                                  │                      │
│     │                               │                                  │                      │
│     ▼                               │                                  │                      │
│   Pilih guru sebagai wali kelas     │                                  │                      │
│     │                               │                                  │                      │
│     └───────────────────────────────→  Cek data                        │                      │
│                                       │                                │                      │
│                                       ▼                               │                      │
│                                     Catat perubahan di                │                      │
│                                     ClassAuditLog:                    │                      │
│                                     • previousTeacherId               │                      │
│                                     • newTeacherId                     │                      │
│                                       │                                │                      │
│                                       ▼                               │                      │
│                                     Update Class.homeroomTeacherId ─────→ UPDATE              │
│                                       │                                │                      │
│                                     INSERT ClassAuditLog ─────────────→ INSERT               │
│                                       │                                │                      │
│   ←───────────────────────────────  Berhasil                          │                      │
│     │                               │                                  │                      │
│     ▼                               │                                  │                      │
│   End                               │                                  │                      │
└─────────────────────────────────────┴──────────────────────────────────┴──────────────────────┘
```

---

## Activity 8: Kenaikan Kelas (Akhir Tahun Ajaran)

```
┌─────────────────────────────────────┬──────────────────────────────────┬──────────────────────┐
│          Operator                   │            System                │      Database        │
├─────────────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│                                     │                                  │                      │
│   Start                             │                                  │                      │
│     │                               │                                  │                      │
│     ▼                               │                                  │                      │
│   Akhiri tahun ajaran aktif         │                                  │                      │
│     │                               │                                  │                      │
│     └───────────────────────────────→  Set isActive=false ──────────────→ UPDATE AcademicYear  │
│                                       │                                │                      │
│                                       ▼                               │                      │
│                                     Buat tahun ajaran baru ────────────→ INSERT AcademicYear   │
│                                       │  (isActive=true)               │                      │
│                                       │                                │                      │
│                                     Buat class untuk kelas baru        │                      │
│                                       │  ──────────────────────────────→ INSERT Class          │
│                                       │                                │                      │
│                                     Pindahkan siswa ke class baru      │                      │
│                                       │  ──────────────────────────────→ UPDATE Student       │
│                                       │    classId                     │                      │
│   ←───────────────────────────────  Selesai                            │                      │
│     │                               │                                  │                      │
│     ▼                               │                                  │                      │
│   Assign wali kelas baru            │                                  │                      │
│     │                               │                                  │                      │
│     ▼                               │                                  │                      │
│   End                               │                                  │                      │
└─────────────────────────────────────┴──────────────────────────────────┴──────────────────────┘
```

---

# Ringkasan Activity Diagram

| No | Activity                  | Aktor Utama       | Interaksi dengan Sistem                                     |
|----|---------------------------|-------------------|-------------------------------------------------------------|
| 1  | Login                     | Semua User        | Validasi kredensial, generate JWT                           |
| 2  | Input Data Akademik       | Guru              | CRUD nilai, kehadiran, prestasi, kesehatan                  |
| 3  | AI Student Summary        | Guru              | Generate ringkasan via LLM, review, simpan                  |
| 4  | AI Draft Deskripsi        | Guru              | Generate draft rapor, edit, finalisasi                       |
| 5  | AI Transition Summary     | Guru              | Generate ringkasan untuk wali kelas baru                    |
| 6  | Preview Buku Induk        | Guru              | Tampilkan data siap salin manual                            |
| 7  | Teacher Assignment        | Administrator     | Assign/ubah wali kelas, audit log                           |
| 8  | Kenaikan Kelas            | Operator          | Akhiri tahun, buat baru, pindahkan siswa                    |
