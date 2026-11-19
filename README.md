# Damantara Studio — website (damantara.my.id)

Situs statis satu file (`index.html`, tanpa build step, tanpa database).
Gaya Geist / Vercel-like: hitam-putih, font Geist, kartu hairline.

## Isi
- Hero Damantara Studio
- Damantara POS — ditandai "Segera hadir" (v2.3 Tauri masih diaudit, jangan dijual dulu)
- Katalog 13 produk software dari audit Shopee 13 Nov 2026
  (`~/workspace/your_files/Laporan-Audit-Shopee-Damantara-Studio-2026-11-13.xlsx`):
  harga = harga Shopee, terjual = data Shopee. Garansi TIDAK ditulis di situs
  karena ada yang bentrok di Shopee (Flexi 25, FlexiSign 19, Digital Factory,
  EngraveLab) — pembeli diarahkan tanya admin via WA.
- Cara beli: WhatsApp 0812-9000-0520 (wa.me/6281290000520), PayPal untuk
  pembeli luar negeri, instal remote.
- Footer: alamat toko, YouTube @damantara-studio, Shopee.

## Deploy — pilih satu
### A. Vercel (yang disukai user, "vercel ala duta")
1. User buat repo GitHub baru (Muse GitHub App tidak bisa buat repo — 403).
   Nama usulan: `damantara-studio-website`.
2. Push folder ini ke repo itu.
3. Di vercel.com/new: import repo itu. (Konektor Vercel MCP met saat ini 403
   untuk scope tim `irulls-projects` — user import sendiri di dashboard,
   seperti SablonAI dulu.)
4. Tambah custom domain `damantara.my.id` + `www.damantara.my.id` di proyek
   Vercel, lalu ikuti DNS yang diminta Vercel di Cloudflare (CNAME/A).
   Verifikasi: buka https://damantara.my.id/ tanpa login — harus 200.

### B. T450 via Caddy (sudah terpasang 19 Nov 2026)
1. Salin folder ini ke T450, misal `~/damantara-website/`.
2. Tambah ke `~/caddy/Caddyfile` (di luar blok `:18080` yang pakai Basic Auth):
   ```
   damantara.my.id, www.damantara.my.id {
     root * /home/damantara/damantara-website
     file_server
   }
   ```
   Blok ini TANPA basic_auth — website memang publik.
3. Tambah ingress di `~/cloudflare/config.yml`:
   ```
   - hostname: damantara.my.id
     service: http://127.0.0.1:80
   - hostname: www.damantara.my.id
     service: http://127.0.0.1:80
   ```
   (Caddy default listen :80 butuh root — ganti ke port tinggi, misal 18081,
   dan arahkan ingress ke situ; atau biarkan Caddy listen 80 via cap_net_bind
   — putuskan saat deploy.)
4. Buat DNS di Cloudflare: CNAME `damantara.my.id` (@) dan `www` ke
   `<tunnel-id>.cfargotunnel.com`, Proxied. Saat ini keduanya BELUM ada
   record sama sekali (cek 20 Nov 2026 ~00:08 WIB: curl 000).
5. Restart Caddy + cloudflared, verifikasi publik tanpa login.

## Catatan
- Jangan klaim POS sudah dijual sebelum audit v2.3 selesai.
- Jangan tulis garansi spesifik per produk sebelum bentrok Shopee dibereskan.
- Harga di situs = harga Shopee 13 Nov 2026; kalau harga Shopee berubah,
  ubah juga di sini.
