# Sequence Diagram
## Longitudinal Student Academic Record Berbasis Artificial Intelligence

Pendahuluan: Sequence Diagram berikut menggambarkan interaksi antar komponen sistem secara detail untuk setiap fitur utama. Setiap diagram menunjukkan urutan pesan antara User, Frontend (Next.js), Backend (Elysia), Database (MySQL via Prisma), dan AI Service (LLM API).

---

## SD-01: Login

```
User                    Frontend                 Backend                  Database
 │                         │                        │                        │
 │   Input username &      │                        │                        │
 │   password              │                        │                        │
 │────────────────────────→│                        │                        │
 │                         │                        │                        │
 │                         │  POST /api/auth/login  │                        │
 │                         │───────────────────────→│                        │
 │                         │                        │                        │
 │                         │                        │  SELECT user           │
 │                         │                        │  WHERE username        │
 │                         │                        │───────────────────────→│
 │                         │                        │                        │
 │                         │                        │  Data user + hash      │
 │                         │                        │◄───────────────────────│
 │                         │                        │                        │
 │                         │                        │  Validasi password     │
 │                         │                        │  (bcrypt/argon2)       │
 │                         │                        │  Generate JWT          │
 │                         │                        │                        │
 │                         │  { token, userData }   │                        │
 │                         │◄───────────────────────│                        │
 │                         │                        │                        │
 │   Dashboard + token     │                        │                        │
 │◄────────────────────────│                        │                        │
 │                         │                        │                        │
 │                         │                        │                        │
 │   [Invalid credentials]  │                        │                        │
 │                         │                        │                        │
 │   Pesan error           │                        │                        │
 │◄────────────────────────│                        │                        │
```

---

## SD-02: Membuat Semester Record

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Pilih siswa +         │                        │                        │
 │  semester              │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  POST /api/students/   │                        │
 │                        │  :id/semester-records  │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  Cek hak akses         │
 │                        │                        │  (homeroom check)      │
 │                        │                        │  Cek duplikasi         │
 │                        │                        │  (studentId+           │
 │                        │                        │   academicYearId+      │
 │                        │                        │   semester)            │
 │                        │                        │                        │
 │                        │                        │  INSERT SemesterRecord │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  Record baru           │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │  { success, data }     │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Semester record       │                        │                        │
 │  tersimpan             │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-03: Input Nilai Mata Pelajaran (Upsert)

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Input nilai           │                        │                        │
 │  (knowledgeScore +     │                        │                        │
 │   skillsScore)         │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  PUT /api/semester-    │                        │
 │                        │  records/:id/subject-  │                        │
 │                        │  scores/:scoreId       │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  Prisma upsert:        │
 │                        │                        │  - Cari SubjectScore   │
 │                        │                        │    (semesterRecordId   │
 │                        │                        │     + subjectName)     │
 │                        │                        │  - Jika ada → UPDATE   │
 │                        │                        │  - Jika tidak → CREATE │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  Data tersimpan        │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │  { success, data }     │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Nilai tersimpan       │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-04: Input Kehadiran (Upsert)

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Input kehadiran       │                        │                        │
 │  (sakit/izin/alpha)    │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  PUT /api/semester-    │                        │
 │                        │  records/:id/attendance│                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  Prisma upsert:        │
 │                        │                        │  - Cari Attendance     │
 │                        │                        │    (semesterRecordId)  │
 │                        │                        │  - Jika ada → UPDATE   │
 │                        │                        │  - Jika tidak → CREATE │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  Data tersimpan        │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │  { success, data }     │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Kehadiran tersimpan   │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-05: Generate AI Student Summary

```
Guru                   Frontend                 Backend                  Database               AI/LLM
 │                        │                        │                        │                     │
 │  Pilih siswa           │                        │                        │                     │
 │───────────────────────→│                        │                        │                     │
 │                        │                        │                        │                     │
 │                        │  POST /api/ai/students │                        │                     │
 │                        │  /:id/summary          │                        │                     │
 │                        │───────────────────────→│                        │                     │
 │                        │                        │                        │                     │
 │                        │                        │  Cek hak akses guru    │                     │
 │                        │                        │                        │                     │
 │                        │                        │  SELECT seluruh        │                     │
 │                        │                        │  SemesterRecord siswa  │                     │
 │                        │                        │  + SubjectScore        │                     │
 │                        │                        │  + Attendance          │                     │
 │                        │                        │  + Achievement         │                     │
 │                        │                        │  + HealthRecord        │                     │
 │                        │                        │───────────────────────→│                     │
 │                        │                        │                        │                     │
 │                        │                        │  Data akademik lengkap │                     │
 │                        │                        │◄───────────────────────│                     │
 │                        │                        │                        │                     │
 │                        │                        │  Susun prompt:         │                     │
 │                        │                        │  "Buat ringkasan       │                     │
 │                        │                        │   perkembangan siswa   │                     │
 │                        │                        │   [nama] berdasarkan   │                     │
 │                        │                        │   data berikut: ..."   │                     │
 │                        │                        │                        │                     │
 │                        │                        │  POST prompt           │                     │
 │                        │                        │──────────────────────────────────────────────→│
 │                        │                        │                        │                     │
 │                        │                        │                        │                     │
 │                        │                        │  Response narasi       │                     │
 │                        │                        │◄──────────────────────────────────────────────│
 │                        │                        │                        │                     │
 │                        │                        │  Simpan AiSummary:     │                     │
 │                        │                        │  - summaryType:        │                     │
 │                        │                        │    STUDENT_SUMMARY     │                     │
 │                        │                        │  - isFinal: false      │                     │
 │                        │                        │  - version: 1          │                     │
 │                        │                        │───────────────────────→│                     │
 │                        │                        │                        │                     │
 │                        │                        │  Draft tersimpan       │                     │
 │                        │                        │◄───────────────────────│                     │
 │                        │                        │                        │                     │
 │                        │  { success, data:     │                        │                     │
 │                        │    summary }           │                        │                     │
 │                        │◄───────────────────────│                        │                     │
 │                        │                        │                        │                     │
 │  Tampilkan hasil AI    │                        │                        │                     │
 │◄───────────────────────│                        │                        │                     │
 │                        │                        │                        │                     │
 │  [Guru review]         │                        │                        │                     │
 │  - Terima              │                        │                        │                     │
 │  - Edit                │                        │                        │                     │
 │  - Regenerate          │                        │                        │                     │
```

---

## SD-06: Regenerate AI Summary (Versioning)

```
Guru                   Frontend                 Backend                  Database               AI/LLM
 │                        │                        │                        │                     │
 │  Klik "Regenerate"     │                        │                        │                     │
 │───────────────────────→│                        │                        │                     │
 │                        │                        │                        │                     │
 │                        │  POST /api/ai-         │                        │                     │
 │                        │  summaries/:id/        │                        │                     │
 │                        │  regenerate            │                        │                     │
 │                        │───────────────────────→│                        │                     │
 │                        │                        │                        │                     │
 │                        │                        │  Cari AiSummary        │                     │
 │                        │                        │  sebelumnya           │                     │
 │                        │                        │  (summaryType +        │                     │
 │                        │                        │   semesterRecordId)    │                     │
 │                        │                        │  Ambil version max     │                     │
 │                        │                        │───────────────────────→│                     │
 │                        │                        │                        │                     │
 │                        │                        │  Version saat ini: 1   │                     │
 │                        │                        │◄───────────────────────│                     │
 │                        │                        │                        │                     │
 │                        │                        │  Ambil ulang data      │                     │
 │                        │                        │  akademik ────────────→│                     │
 │                        │                        │◄───────────────────────│                     │
 │                        │                        │                        │                     │
 │                        │                        │  Prompt → LLM ──────────────────────────────→│
 │                        │                        │◄──────────────────────────────────────────────│
 │                        │                        │                        │                     │
 │                        │                        │  Simpan AiSummary:     │                     │
 │                        │                        │  - version: 2          │                     │
 │                        │                        │  - isFinal: false      │                     │
 │                        │                        │───────────────────────→│                     │
 │                        │                        │                        │                     │
 │  Tampilkan hasil baru  │                        │                        │                     │
 │◄───────────────────────│                        │                        │                     │
 │                        │                        │                        │                     │
 │  [Guru bisa bandingkan │                        │                        │                     │
 │   versi 1 vs versi 2]  │                        │                        │                     │
```

---

## SD-07: Finalisasi AI Summary (Human-in-the-loop)

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Klik "Simpan"         │                        │                        │
 │  (setelah review)      │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  PUT /api/ai-          │                        │
 │                        │  summaries/:id         │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  UPDATE AiSummary:     │
 │                        │                        │  - content (jika diedit)│
 │                        │                        │  - isFinal = true      │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  Final tersimpan       │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │  { success, final }    │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Hasil final siap      │                        │                        │
 │  digunakan             │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-08: Melihat Longitudinal Student Profile

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Klik detail siswa     │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  GET /api/students     │                        │
 │                        │  /:id/profile          │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  Cek hak akses         │
 │                        │                        │                        │
 │                        │                        │  SELECT Student        │
 │                        │                        │  WHERE id              │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  SELECT Class (current)│
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  SELECT SemesterRecord │
 │                        │                        │  WHERE studentId       │
 │                        │                        │  ORDER BY academicYear │
 │                        │                        │  JOIN SubjectScore     │
 │                        │                        │  JOIN Attendance       │
 │                        │                        │  JOIN Achievement      │
 │                        │                        │  JOIN HealthRecord     │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  Data lengkap:         │
 │                        │                        │  - Biodata             │
 │                        │                        │  - Riwayat kelas       │
 │                        │                        │  - Semua semester      │
 │                        │                        │  - Nilai per semester  │
 │                        │                        │  - Kehadiran           │
 │                        │                        │  - Prestasi            │
 │                        │                        │  - Kesehatan           │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │  { profile, records }  │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Tampilkan profile     │                        │                        │
 │  + timeline            │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-09: Preview Buku Induk

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Buka Preview Buku     │                        │                        │
 │  Induk                 │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  GET /api/students     │                        │
 │                        │  /:id/buku-induk-      │                        │
 │                        │  preview               │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  Cek hak akses         │
 │                        │                        │                        │
 │                        │                        │  SELECT Student        │
 │                        │                        │  dengan seluruh        │
 │                        │                        │  SemesterRecord        │
 │                        │                        │  + SubjectScore        │
 │                        │                        │  + Attendance          │
 │                        │                        │  + Achievement         │
 │                        │                        │  + HealthRecord        │
 │                        │                        │  (all time)            │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  Data lengkap          │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │                        │  Format ulang sesuai   │
 │                        │                        │  format Buku Induk     │
 │                        │                        │                        │
 │                        │  { preview: {          │                        │
 │                        │    biodata: ...,       │                        │
 │                        │    nilai: [...],       │                        │
 │                        │    kehadiran: [...],   │                        │
 │                        │    prestasi: [...],    │                        │
 │                        │    kesehatan: [...]    │                        │
 │                        │  }                     │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Tampilkan preview     │                        │                        │
 │  sesuai urutan Buku    │                        │                        │
 │  Induk                 │                        │                        │
 │◄───────────────────────│                        │                        │
 │                        │                        │                        │
 │  [Guru menyalin ke     │                        │                        │
 │   Buku Induk manual]   │                        │                        │
```

---

## SD-10: Teacher Assignment (dengan Audit Log)

```
Admin                  Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Assign wali kelas     │                        │                        │
 │  ke kelas tertentu     │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  PATCH /api/classes    │                        │
 │                        │  /:id/homeroom-teacher │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  SELECT Class (current)│
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  homeroomTeacherId:    │
 │                        │                        │  oldTeacherId          │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │                        │  BEGIN Transaction     │
 │                        │                        │                        │
 │                        │                        │  UPDATE Class SET      │
 │                        │                        │  homeroomTeacherId     │
 │                        │                        │  = newTeacherId        │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  INSERT ClassAuditLog  │
 │                        │                        │  (classId,             │
 │                        │                        │   previousTeacherId,   │
 │                        │                        │   newTeacherId,        │
 │                        │                        │   changedById)         │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  COMMIT                │
 │                        │                        │                        │
 │                        │                        │  { success }           │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │  { success }           │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Wali kelas berhasil   │                        │                        │
 │  diubah                │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-11: Academic Year Activation

```
Admin                  Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Aktifkan tahun        │                        │                        │
 │  ajaran baru           │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  PATCH /api/academic-  │                        │
 │                        │  years/:id/activate    │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  BEGIN Transaction     │
 │                        │                        │                        │
 │                        │                        │  UPDATE AcademicYear   │
 │                        │                        │  SET isActive=false    │
 │                        │                        │  WHERE isActive=true   │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  UPDATE AcademicYear   │
 │                        │                        │  SET isActive=true     │
 │                        │                        │  WHERE id = :id        │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  COMMIT                │
 │                        │                        │                        │
 │                        │  { success }           │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Tahun ajaran aktif    │                        │                        │
 │  berubah               │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-12: Administrative Validation Status

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Cek status            │                        │                        │
 │  kelengkapan data      │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  GET /api/students     │                        │
 │                        │  /:id/validation-status│                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  SELECT SemesterRecord │
 │                        │                        │  WHERE studentId       │
 │                        │                        │  JOIN SubjectScore     │
 │                        │                        │  LEFT JOIN Attendance  │
 │                        │                        │  LEFT JOIN HealthRecord│
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  Hasil query           │
 │                        │                        │◄───────────────────────│
 │                        │                        │                        │
 │                        │                        │  Business logic:       │
 │                        │                        │  Per semester:         │
 │                        │                        │  ✓ Nilai lengkap?      │
 │                        │                        │  ✓ Kehadiran terisi?   │
 │                        │                        │  ✓ Kesehatan terisi?   │
 │                        │                        │                        │
 │                        │  { validation: [       │                        │
 │                        │    { semester: 1,      │                        │
 │                        │      status: {         │                        │
 │                        │        subjectScores:  │                        │
 │                        │          "complete",   │                        │
 │                        │        attendance:     │                        │
 │                        │          "incomplete", │                        │
 │                        │        healthRecord:   │                        │
 │                        │          "incomplete"  │                        │
 │                        │      } }, ...          │                        │
 │                        │  ] }                   │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Tampilkan status      │                        │                        │
 │  per semester          │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

## SD-13: Dashboard Summary

```
Guru                   Frontend                 Backend                  Database
 │                        │                        │                        │
 │  Buka Dashboard        │                        │                        │
 │───────────────────────→│                        │                        │
 │                        │                        │                        │
 │                        │  GET /api/dashboard    │                        │
 │                        │  /summary              │                        │
 │                        │───────────────────────→│                        │
 │                        │                        │                        │
 │                        │                        │  [Role: GURU]           │
 │                        │                        │  SELECT COUNT Student   │
 │                        │                        │  WHERE classId IN       │
 │                        │                        │  (kelas yang diampu)    │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  SELECT AcademicYear    │
 │                        │                        │  WHERE isActive=true    │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  SELECT SemesterRecord │
 │                        │                        │  (hitung progress      │
 │                        │                        │   pengisian per kelas)  │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │                        │  SELECT AiSummary      │
 │                        │                        │  WHERE isFinal=false   │
 │                        │                        │  (draft yang belum     │
 │                        │                        │   direview)            │
 │                        │                        │───────────────────────→│
 │                        │                        │                        │
 │                        │  { dashboard: {        │                        │
 │                        │    totalStudents: 32,  │                        │
 │                        │    activeYear:         │                        │
 │                        │      "2025/2026",      │                        │
 │                        │    semesterProgress:   │                        │
 │                        │      85,               │                        │
 │                        │    pendingAiDrafts: 5  │                        │
 │                        │  } }                   │                        │
 │                        │◄───────────────────────│                        │
 │                        │                        │                        │
 │  Tampilkan dashboard   │                        │                        │
 │◄───────────────────────│                        │                        │
```

---

# Ringkasan Sequence Diagram

| No | Diagram                    | Aktor Utama | Komponen yang Terlibat                          |
|----|----------------------------|-------------|-------------------------------------------------|
| 1  | Login                      | Semua User  | Frontend → Backend → Database                   |
| 2  | Create Semester Record     | Guru        | Frontend → Backend → Database                   |
| 3  | Input Nilai (Upsert)       | Guru        | Frontend → Backend → Database                   |
| 4  | Input Kehadiran (Upsert)   | Guru        | Frontend → Backend → Database                   |
| 5  | AI Student Summary         | Guru        | Frontend → Backend → Database → LLM API         |
| 6  | Regenerate AI (Versioning) | Guru        | Frontend → Backend → Database → LLM API         |
| 7  | Finalisasi AI              | Guru        | Frontend → Backend → Database                   |
| 8  | Student Profile            | Guru        | Frontend → Backend → Database                   |
| 9  | Preview Buku Induk         | Guru        | Frontend → Backend → Database                   |
| 10 | Teacher Assignment         | Admin       | Frontend → Backend → Database (transaksi)       |
| 11 | Academic Year Activation   | Admin       | Frontend → Backend → Database (transaksi)       |
| 12 | Validation Status          | Guru        | Frontend → Backend → Database                   |
| 13 | Dashboard Summary          | Guru        | Frontend → Backend → Database                   |
