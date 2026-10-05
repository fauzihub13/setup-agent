# Setup Agent

Template default `AGENTS.md` untuk AI coding agent — technology-agnostic, siap
copy ke proyek mana pun.

## Apa ini

`AGENTS.md` adalah satu file yang memberitahu AI coding agent (opencode,
Cursor, Copilot, Claude Code, dan agent lain yang membaca `AGENTS.md`) cara
bekerja di dalam sebuah repo: cara mengelola context, memakai memory proyek,
mengeksplorasi kode, mengubah file, validasi, dan berinteraksi dengan Git.

File ini sengaja tidak terikat bahasa atau framework. Agent diwajibkan
memahami stack dan konvensi repo dulu, bukan berasumsi.

## Prinsip inti

> **Muat context seminimal mungkin untuk menyelesaikan tugas dengan benar.
> Simpan pengetahuan yang bisa dipakai ulang. Jangan buang context untuk hal
> yang tidak relevan.**

Tiga aturan wajib:

1. **Memory First** — kalau `memory/` ada, baca `memory/INDEX.md` dulu sebelum
   menjelajah source.
2. **Context Is a Budget** — baca seperlunya, tapi jangan korbankan kebenaran
   demi hemat token.
3. **Git Is Explicit** — agent tidak commit/push kecuali diminta eksplisit.

## Cara pakai

1. Copy `AGENTS.md` ke root proyek:

   ```sh
   cp AGENTS.md /path/ke/proyek-anda/AGENTS.md
   ```

2. (Opsional) Buat memory proyek dan pastikan di-ignore Git:

   ```sh
   mkdir -p memory
   printf '/memory/\n' >> .gitignore
   ```

   Lalu isi `memory/INDEX.md` sebagai pintu masuk daftar pengetahuan proyek.
   Agent akan memelihara file ini otomatis setelah tugas yang menghasilkan
   pengetahuan baru.

3. Sesuaikan bagian yang spesifik proyek (contoh: perintah test, build, lint)
   kalau perlu. Selebihnya bisa dibiarkan apa adanya.

## Isi AGENTS.md

| Bagian | Isi |
| --- | --- |
| 1 | Tiga aturan wajib (memory, context, Git) |
| 2 | Workflow standar dari request sampai validasi |
| 3 | Persistent project memory: entry point, batasan, cara rawat |
| 4 | Eksplorasi repo: search dulu, baca secukupnya |
| 5 | Implementasi: konvensi, scope, dependency, config, API, gotchas |
| 6 | Validasi |
| 7 | Safety: preservasi perubahan user, operasi destruktif |
| 8 | Kebijakan Git |
| 9 | Kapan bertanya ke user |
| 10 | Format final response |
| 11 | Checklist sebelum selesai |

## Manfaat

- Hemat context: agent tidak membaca seluruh repo tiap tugas.
- Konsisten: semua agent mengikuti aturan yang sama.
- Punya memori: pengetahuan penting tersimpan antar sesi.
- Aman: perubahan user dan operasi Git terkendali.