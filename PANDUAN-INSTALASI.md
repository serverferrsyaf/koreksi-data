# Panduan Instalasi — Koreksi Data Keluarga Pegawai

Hasil akhir: sebuah alamat web (misalnya `https://namaanda.github.io/koreksi-data/`) yang bisa dibuka semua pegawai dari HP atau komputer, dengan login dan data tersimpan aman.

**Cara kerjanya (supaya tidak bingung):**
- **GitHub Pages** = tempat "menayangkan" halaman webnya (gratis).
- **Firebase** = tempat menyimpan data dan mengatur login (gratis untuk skala ini).
- File Excel **tidak pernah** disimpan di GitHub. Data hanya ada di Firebase.

Waktu pengerjaan sekitar 30–45 menit. Semua langkah dikerjakan lewat browser, tanpa menginstal program apa pun.

---

## A. Yang perlu disiapkan

1. Akun **Google** (Gmail) — untuk Firebase.
2. Akun **GitHub** — daftar gratis di https://github.com/signup.
3. File ini (hasil unduhan): `index.html`, `firebase-config.js`, `firestore.rules`, `.gitignore`, `PANDUAN-INSTALASI.md`.
4. File Excel `DATA KELUARGA.xlsx`.
5. Siapkan **3 kata sandi kuat** (minimal 8 karakter, campur huruf besar, kecil, angka) untuk akun PNS, PPPK, dan superadmin. Firebase menolak sandi kurang dari 6 karakter, jadi sandi `admin` tidak bisa dipakai, dan sandi itu memang terlalu mudah ditebak untuk data pribadi. Contoh pola: `Pns#Okt2026!`.

---

## B. Membuat "gudang data" di Firebase

### B1. Buat project
1. Buka https://console.firebase.google.com lalu login dengan akun Google.
2. Klik **Create a project** (atau **Add project**).
3. Nama project: misalnya `koreksi-data-pegawai` → **Continue**.
4. Matikan Google Analytics (tidak perlu) → **Create project** → tunggu → **Continue**.

### B2. Aktifkan login
1. Menu kiri: **Build → Authentication** → **Get started**.
2. Tab **Sign-in method** → pilih **Email/Password** → aktifkan **Enable** (yang pertama saja, bukan "Email link") → **Save**.

### B3. Buat 3 akun
Masih di Authentication, buka tab **Users** → **Add user**. Buat tepat 3 akun ini:

| Email (ketik persis) | Password |
|---|---|
| `pns@pegawai.app` | sandi kuat pilihan Anda |
| `pppk@pegawai.app` | sandi kuat pilihan Anda |
| `superadmin@pegawai.app` | sandi kuat pilihan Anda (jangan sama dengan yang lain) |

> Email ini hanya nama akun, bukan email sungguhan. Di halaman web pegawai cukup mengetik **PNS**, **PPPK**, atau **superadmin** sebagai "Nama akun".

Lalu matikan pendaftaran mandiri: tab **Settings → User actions** → hilangkan centang **Enable create (sign-up)** → **Save**. (Walaupun terlewat, aturan keamanan di B6 tetap menolak orang selain 3 akun ini.)

### B4. Buat database
1. Menu kiri: **Build → Firestore Database** → **Create database**.
2. Lokasi: pilih **asia-southeast2 (Jakarta)** → **Next**.
3. Pilih **Start in production mode** → **Create**.

### B5. Ambil kode pengaturan lalu isi `firebase-config.js`
1. Klik ikon roda gigi di kiri atas → **Project settings**.
2. Gulir ke **Your apps** → klik ikon web **`</>`**.
3. Nama app: `web` (jangan centang Firebase Hosting) → **Register app**.
4. Akan tampil blok `const firebaseConfig = { apiKey: "...", ... }`. Salin nilainya.
5. Buka file **`firebase-config.js`** dengan Notepad, ganti semua tulisan `ISI_...` dengan nilai tadi. Contoh hasil:
   ```js
   export const FIREBASE_CONFIG = {
     apiKey: "AIzaSyD...abc",
     authDomain: "koreksi-data-pegawai.firebaseapp.com",
     projectId: "koreksi-data-pegawai",
     storageBucket: "koreksi-data-pegawai.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abcdef"
   };
   ```
   Biarkan `EMAIL_DOMAIN = "pegawai.app"` apa adanya. Simpan file.

> Kode ini aman berada di GitHub publik. Fungsinya hanya menunjukkan ke project mana halaman terhubung; yang melindungi data adalah login dan aturan di B6.

### B6. Pasang aturan keamanan (SANGAT PENTING)
1. Di **Firestore Database**, buka tab **Rules**.
2. Hapus semua isinya, lalu salin-tempel seluruh isi file **`firestore.rules`**.
3. Klik **Publish**.

Aturan ini memastikan: hanya 3 akun tadi yang bisa membaca data; akun PNS hanya bisa melihat dan menyimpan data PNS (PPPK sebaliknya); pegawai hanya bisa mengisi 4 isian koreksi dan tidak bisa menimpa data induk; nomor telpon dan nama penginput wajib valid.

---

## C. Menayangkan di GitHub

### C1. Buat repository
1. Login ke https://github.com → klik **+** (kanan atas) → **New repository**.
2. Repository name: `koreksi-data` (huruf kecil, tanpa spasi).
3. Pilih **Public** (GitHub Pages gratis memerlukan Public).
4. Klik **Create repository**.

### C2. Unggah file
1. Di halaman repository, klik **uploading an existing file**.
2. Seret file: `index.html`, `firebase-config.js` (yang sudah Anda isi), `firestore.rules`, `.gitignore`, `PANDUAN-INSTALASI.md`.
3. **Jangan unggah file Excel.**
4. Klik **Commit changes**.

### C3. Aktifkan GitHub Pages
1. **Settings** (tab repository) → menu kiri **Pages**.
2. **Source**: *Deploy from a branch*. **Branch**: `main`, folder `/ (root)` → **Save**.
3. Tunggu 1–3 menit, muat ulang. Muncul tulisan *Your site is live at* `https://NAMAANDA.github.io/koreksi-data/`. Itulah alamat yang dibagikan ke pegawai.

### C4. Izinkan alamat tersebut di Firebase
1. Kembali ke Firebase → **Authentication → Settings → Authorized domains** → **Add domain**.
2. Isi `NAMAANDA.github.io` (tanpa `https://` dan tanpa `/koreksi-data`) → **Add**.

---

## D. Pemakaian pertama (oleh superadmin)

1. Buka alamat website, isi **Nama lengkap penginput (nama asli)**, Nama akun `superadmin`, dan sandinya.
2. Buka tab **Kelola Data** → pilih `DATA KELUARGA.xlsx`.
3. Periksa pemetaan kolom (NIP, Nama, Status, Keterangan, Nomor telpon). Pilih kolom yang sesuai atau biarkan **(buat kolom baru)** bila belum ada di sheet.
4. Klik **Simpan ke Database** dan tunggu sampai muncul "Data berhasil disimpan". File besar bisa butuh beberapa menit; jangan tutup halaman.
5. Cek tab **Dashboard** dan **Cari & Koreksi**.
6. Bagikan alamat web beserta nama akun dan sandi: akun **PNS** untuk pegawai PNS, akun **PPPK** untuk pegawai PPPK. Kirim sandi lewat jalur internal (jangan ditempel di grup terbuka).

Mengunduh hasil: tab **Kelola Data → Unduh Excel**. Hasilnya memuat koreksi terbaru, plus kolom *Diubah Oleh*, *Akun*, dan *Waktu Ubah*.

Mengubah isi `firebase-config.js` atau `index.html` kemudian: buka file di GitHub → ikon pensil → ubah → **Commit changes**.

---

## E. Pengamanan yang aktif dan saran tambahan

**Sudah aktif:**
- Login sungguhan oleh Firebase (bukan sandi di dalam kode); hanya 3 akun yang diizinkan.
- Pemisahan hak akses PNS / PPPK / superadmin ditegakkan di server lewat `firestore.rules`.
- Nama penginput wajib (minimal 3 huruf) dan tercatat di tiap perubahan.
- Keluar otomatis setelah 15 menit tidak aktif; sesi tidak tersimpan saat halaman ditutup.
- Penundaan setelah 5 kali salah sandi; halaman memakai `noindex` agar tidak muncul di mesin pencari.
- File Excel dan data tidak ada di GitHub.

**Sangat disarankan:**
1. **Batasi API key** (30 detik): https://console.cloud.google.com → pilih project Anda → **APIs & Services → Credentials** → klik *Browser key* → **Website restrictions** → tambahkan `https://NAMAANDA.github.io/*` → Save.
2. **Ganti sandi** berkala dan saat ada pegawai yang berhenti/mutasi (Authentication → Users → titik tiga → *Reset password*, atau hapus lalu buat ulang).
3. Pegawai berbagi akun PNS/PPPK, jadi nama penginput di login adalah satu-satunya penanda siapa yang mengedit. Ingatkan agar tidak memakai nama orang lain. Bila perlu pelacakan per orang, buat akun individu (perlu pengembangan lanjutan).
4. **Cadangan**: unduh Excel lewat superadmin secara berkala dan simpan di tempat aman.
5. Simpan file unduhan dengan hati-hati karena berisi data pribadi.

**Batas penggunaan gratis (paket Spark):** 50.000 pembacaan dan 20.000 penulisan per hari. Setiap pegawai yang membuka halaman membaca seluruh data kelompoknya, jadi bila ribuan pegawai membuka bersamaan dalam sehari, kuota bisa habis dan aplikasi berhenti sampai hari berikutnya. Atasi dengan mengatur jadwal pengisian per unit, atau memakai paket berbayar (Blaze) dengan batas anggaran kecil.

---

## F. Jika ada masalah

| Gejala | Penyebab / solusi |
|---|---|
| Halaman bertulisan "Pengaturan belum diisi" | `firebase-config.js` belum diisi atau belum diunggah ulang ke GitHub (B5). |
| Halaman kosong / 404 | Tunggu 3 menit setelah mengaktifkan Pages; pastikan file bernama persis `index.html`. |
| "Nama akun atau kata sandi salah" | Pastikan akun dibuat dengan email `pns@pegawai.app`, dst. (B3), dan sandi benar. |
| Login berhasil tapi muncul "Akses ditolak" | Aturan di B6 belum di-*Publish*, atau email akun salah ketik. |
| Gagal login dengan pesan domain tidak diizinkan | Tambahkan `NAMAANDA.github.io` di Authorized domains (C4). |
| Tombol **Simpan Data** tidak menyala | Pilih Sesuai/Tidak Sesuai dan isi nomor telpon (8–15 digit, boleh diawali +). |
| Unggah Excel gagal di tengah jalan | Cek koneksi, coba lagi; data lama diganti hanya setelah unggahan baru selesai. |
| Perubahan di GitHub belum tampil | Tunggu 1–2 menit lalu muat ulang dengan Ctrl+F5. |
