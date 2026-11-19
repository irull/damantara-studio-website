# Deploy ke Coolify di T450 — runbook

Keadaan 20 Nov 2026 ~00:32 WIB: Coolify v4.7.6 LIVE di T450
(http://127.0.0.1:8000, login page 200). Admin: irulsoekamti94@gmail.com,
password acak di `/home/damantara/.coolify-admin-pass` di T450 (0600).
Akses dari Jakarta: nyalakan Tailscale di HP → http://100.99.120.71:8000.
Repo lokal website sudah commit `bea8a09` di `~/workspace/damantara-studio-website/`
— tinggal push begitu repo GitHub `irull/damantara-studio-website` dibuat user
(Muse GitHub App tidak bisa buat repo).

## 1. Push repo
```
cd ~/workspace/damantara-studio-website
git remote add origin https://github.com/irull/damantara-studio-website.git
git push -u origin main   # atau master, sesuai branch default repo baru
```
(Butuh GitHub App terpasang di repo baru itu; push via konektor GitHub met.)

## 2. Sambung repo ke Coolify (di dashboard)
1. Sources → tambah GitHub App / Private Repository (ikuti OAuth di dashboard).
2. Projects → New Project `damantara-studio` → New Resource → Public Repository
   (atau Private kalau repo-nya private) → pilih repo `irull/damantara-studio-website`.
3. Build Pack: Nixpacks, Static Site — publish directory `/` (index.html di root).
   Tidak ada build command (situs statis satu file).
4. Deploy. Cek log build di dashboard sampai status Running/Healthy.

## 3. Domain damantara.my.id + www
1. Di resource Coolify → Domains → tambah `damantara.my.id` dan
   `www.damantara.my.id`. Coolify (Traefik, port 80/443) yang urus sertifikat
   — TAPI Traefik hanya bisa terima traffic kalau traffic sampai ke T450:80.
   Jalur yang dipakai: Cloudflare Tunnel (bukan port-forward router).
2. Di T450, tambah ke `~/cloudflare/config.yml` (ingress, sebelum baris `service: http_status:404`):
   ```
   - hostname: damantara.my.id
     service: http://127.0.0.1:80
   - hostname: www.damantara.my.id
     service: http://127.0.0.1:80
   ```
   Catatan: ai/chat tetap lewat Caddy :18080 (Basic Auth) — jangan diubah.
   Coolify/Traefik di :80 TIDAK bentrok dengan Caddy (18080, localhost saja).
3. Restart cloudflared (satu proses; jaga jangan dobel — `pkill -f cloudflared`
   lalu jalankan `~/cloudflare/run-cloudflared.sh`).
4. DNS di Cloudflare: CNAME `damantara.my.id` (@) dan `www` ke
   `<tunnel-id>.cfargotunnel.com`, Proxied. (Saat ini keduanya belum ada record.)
5. Verifikasi publik tanpa login: `curl -s -o /dev/null -w "%{http_code}" https://damantara.my.id/`
   harus 200, dan `https://www.damantara.my.id/` juga 200.

## 4. Setelah live
- Cek RAM T450 (`free -h`): Coolify idle ~1GB; kalau `available` < 1GB, kabari user.
- Jangan ubah apa pun di ai/chat/room saat deploy website.
- Kalau user mau ganti password admin Coolify: di dashboard, klik avatar →
  Change Password (bukan lewat file `.coolify-admin-pass`).
