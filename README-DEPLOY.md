# Nala Chatbot — Panduan Deploy ke Vercel

Nala Chatbot adalah chatbot belajar AI untuk siswa SMP **Budi Mulia Karawang** dengan tampilan terminal retro (CRT). Panduan ini membantu kamu mempublikasikannya **permanen di internet gratis** lewat Vercel.

Ada 2 mode kerja:

| Mode | Kapan aktif | Kemampuan |
| --- | --- | --- |
| **Mode Dasar** | Tanpa API key (langsung jalan) | Semua perintah (/topik, /kuis, /bantuan, dll), bank jawaban topik AI, kuis 5 soal dengan penilaian |
| **Mode AI Penuh** | Jika kamu mengisi API key | Semua kemampuan Mode Dasar **plus** jawaban AI bebas untuk pertanyaan apa pun tentang AI |

Bilah status bawah di aplikasi akan menunjukkan mode aktif: `AI: ONLINE` (AI penuh) atau `AI: MODE DASAR`.

---

## Langkah 1 — Siapkan file

1. Ekstrak `nala-chatbot-vercel.zip`.
2. Masuk ke folder hasil ekstrak:

```bash
cd nala-chatbot-vercel
```

## Langkah 2 — Deploy ke Vercel

Pilih salah satu cara berikut.

### Cara A: Lewat GitHub + Vercel (paling mudah, bisa auto-update)

1. Buat akun gratis di [github.com](https://github.com) dan [vercel.com](https://vercel.com) (login pakai akun GitHub juga bisa).
2. Buat repository baru di GitHub, misal `nala-chatbot`, lalu unggah semua isi folder ini (drag & drop di halaman web GitHub juga bisa, atau pakai git):

```bash
git init
git add .
git commit -m "Nala Chatbot pertama"
git branch -M main
git remote add origin https://github.com/USERNAME/nala-chatbot.git
git push -u origin main
```

3. Buka [vercel.com/new](https://vercel.com/new), pilih repository `nala-chatbot`.
4. Vercel otomatis mendeteksi Next.js. **Biarkan semua setting default**, lalu klik **Deploy**.
5. Tunggu 1-2 menit. Selesai! Kamu dapat alamat permanen seperti `https://nala-chatbot.vercel.app`.

Setelah ini, setiap kali kamu update kode di GitHub, Vercel otomatis menerbitkan versi baru.

### Cara B: Lewat Vercel CLI (tanpa GitHub)

```bash
npm install -g vercel
cd nala-chatbot-vercel
vercel          # jawab pertanyaan login & proyek, ikuti petunjuk
vercel --prod   # terbitkan permanen
```

## Langkah 3 (opsional) — Aktifkan Otak AI Penuh

Tanpa API key, Nala berjalan di Mode Dasar. Untuk jawaban AI yang bebas:

1. Dapatkan API key dari salah satu provider:
   - **Z.ai** (ada model gratis seperti `glm-4-flash`): daftar di [z.ai](https://z.ai) → buka API platform → buat API key.
   - **OpenAI** (berbayar): [platform.openai.com](https://platform.openai.com) → API keys.
2. Di dashboard Vercel: pilih proyek **Settings → Environment Variables**, tambahkan:

   | Name | Value | Wajib? |
   | --- | --- | --- |
   | `ZAI_API_KEY` | kunci API dari Z.ai | salah satu wajib |
   | `ZAI_MODEL` | `glm-4-flash` | tidak (default) |
   | `OPENAI_API_KEY` | kunci API dari OpenAI | alternatif Z.ai |
   | `OPENAI_MODEL` | `gpt-4o-mini` | tidak (default) |

   Untuk percobaan di komputer sendiri, salin `.env.example` menjadi `.env.local` lalu isi nilainya.
3. **Redeploy** (tab Deployments → Redeploy) agar variabel baru aktif.
4. Buka aplikasinya — bilah status kini menampilkan `AI: ONLINE`.

## Langkah 4 (opsional) — Pakai domain sekolah

Di Vercel: **Settings → Domains → Add** → masukkan domain kamu (misal `nala.smpbudimuliasch.id`), lalu ikuti petunjuk pengaturan DNS dari Vercel.

---

## Mengubah isi chatbot

| Yang ingin diubah | File | Bagian |
| --- | --- | --- |
| Nama sekolah / tagline | `src/app/page.tsx` | cari `Budi Mulia Karawang` |
| Kepribadian & aturan AI | `src/app/api/chat/route.ts` | konstanta `SYSTEM_PROMPT` |
| Soal kuis Mode Dasar | `src/app/api/chat/route.ts` | konstanta `KUIS` |
| Bank jawaban Mode Dasar | `src/app/api/chat/route.ts` | fungsi `cariJawaban` |
| Warna & gaya terminal | `src/app/globals.css` dan `src/app/page.tsx` | class `text-[#3dff7c]` dsb. |

Setelah mengubah file, jalankan `npm run dev` untuk melihat hasilnya di komputer, atau push ke GitHub agar Vercel menerbitkan ulang otomatis.

## Struktur proyek

```
nala-chatbot-vercel/
├── package.json              # daftar dependensi & perintah npm
├── next.config.ts
├── postcss.config.mjs
├── tailwind (via @tailwindcss/postcss v4)
├── public/                   # aset statis
└── src/
    ├── app/
    │   ├── page.tsx          # UI terminal (chat, boot, perintah)
    │   ├── layout.tsx        # metadata halaman
    │   ├── globals.css       # efek CRT, scanlines, glow
    │   └── api/chat/route.ts # backend: mode AI penuh + Mode Dasar
    ├── components/ui/        # komponen toast
    ├── hooks/
    └── lib/
```

## Pertanyaan yang sering muncul

**Apakah gratis?** Ya. Vercel punya paket Hobby gratis, Z.ai punya model gratis, dan Mode Dasar tidak butuh apa pun.

**Apakah data siswa disimpan?** Tidak. Percakapan hanya berjalan antara browser siswa dan server; tidak ada database.

**Aman untuk anak-anak?** Prompt sistem membatasi Nala pada topik AI, menolak konten tidak pantas, dan menganjurkan cek sumber terpercaya. Tetap anjurkan siswa didampingi sesuai kebijakan sekolah.

**Build gagal / halaman 405?** Pastikan repository berisi file ini apa adanya (terutama `package.json`, `next.config.ts`, dan folder `src`) dan memakai Node.js 20+.

**Domain `https://nala-chatbot.vercel.app` menampilkan 404 "NOT_FOUND"?** Itu artinya di alamat tersebut BELUM ada deployment yang berhasil. Solusinya: selesaikan deploy (Langkah 2), lalu cek nama proyek di dashboard Vercel. Nama proyek menentukan alamat: proyek bernama `nala-chatbot` otomatis mendapat `https://nala-chatbot.vercel.app`. Jika nama proyekmu berbeda (misal `nala-chatbot-xyz`), alamat yang benar tertulis di tab **Deployments** atau tombol **Visit** di dashboard. Bisa juga diubah di **Settings → General → Project Name**, lalu redeploy.

Selamat mencoba, dan semoga siswa-siswa makin semangat belajar AI!
