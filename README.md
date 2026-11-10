# VarroVGK_Member
Starter website member berbayar: Next.js + Supabase + Midtrans.

## Fitur
- Login/registrasi email dan password.
- Role admin/member.
- Dashboard album dan pemutar video.
- Upload video melalui HP (batas awal 100 MB/file).
- Bucket privat dan signed URL video (5 menit).
- Pembayaran manual dengan upload bukti dan persetujuan admin.
- Pembayaran otomatis Midtrans Snap + webhook signature.
- Pilihan paket 7, 30, 90 hari, dan permanent setelah pembayaran dikonfirmasi.

## Persiapan
1. Butuh Node.js 20+, project Supabase, dan akun Midtrans.
2. `npm install` lalu salin `.env.example` menjadi `.env.local` dan isi nilainya.
3. Jalankan `supabase/schema.sql` di Supabase SQL Editor.
4. Buat dua bucket Storage privat: `member-videos` dan `payment-proofs`.
5. Daftar akun pertama di website. Jadikan admin melalui SQL:
   `update public.profiles set role='admin' where email='EMAIL_KAMU';`
6. Jalankan `npm run dev`, buka `http://localhost:3000`.

## Midtrans
Gunakan Sandbox dahulu. Atur notification URL ke `https://DOMAIN/api/payments/midtrans-webhook`. Uji seluruh status transaksi sebelum beralih ke kredensial produksi.

## Deploy
Deploy ke Railway/Vercel/host Node.js. Masukkan semua environment variable. Set `NEXT_PUBLIC_SITE_URL` ke domain HTTPS. Jangan pernah mengirim `SUPABASE_SERVICE_ROLE_KEY` atau `MIDTRANS_SERVER_KEY` ke browser.

## Catatan
Kode ini adalah fondasi awal dan perlu diuji serta diaudit sebelum menerima uang sungguhan. Verifikasi pembayaran manual dengan mutasi rekening, bukan hanya gambar bukti. Siapkan backup, kebijakan privasi, pembatasan percobaan login, rate limiting, dan pemeriksaan hukum/pajak. Upload hanya video yang dimiliki atau memiliki izin distribusi; jangan unggah konten eksploitasi atau seksual yang melibatkan anak. Signed URL membatasi akses langsung tetapi tidak dapat mencegah perekaman layar.

## Pilihan paket
Harga contoh ada di `.env.example`: 7 hari Rp15.000, 30 hari Rp45.000, 90 hari Rp100.000, permanent Rp250.000. Ganti nilai `PLAN_PRICE_*` dan `NEXT_PUBLIC_PLAN_PRICE_*` agar sesuai. Harga di server yang menjadi acuan validasi pembayaran.

Jika database sudah pernah dibuat dari schema versi sebelumnya, jalankan migrasi SQL berikut:
```sql
alter table public.payments add column if not exists plan_key text not null default '7d';
alter table public.payments add column if not exists duration_days integer;
alter table public.subscriptions alter column ends_at drop not null;
```
Untuk akun permanent, `ends_at` bernilai NULL.
