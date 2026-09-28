[README.md](https://github.com/user-attachments/files/32726754/README.md)
# E-Juri Rewa Male — V8 Tingkat Peserta & Single Login

## Pengembangan utama
- Jumlah juri tetap **fleksibel**: nomor juri dapat 1, 2, 3, 4, 5, dan seterusnya.
- Pada **Tambah/Edit User/Juri**, Admin dapat memilih:
  - jenis lomba yang dinilai;
  - satu atau beberapa **tingkat peserta**: **Temu Minggu, Sekami, Taruk, OMK**.
- Penugasan juri disimpan per kombinasi **Lomba + Tingkat**, sehingga Juri 1 pada Temu Minggu tidak bercampur dengan Juri 1 pada OMK.
- Peserta memiliki tingkat masing-masing. **Hasil, ranking, nilai juri, rekap, export, dashboard, dan halaman Live dipisahkan berdasarkan tingkat**.
- Setiap akun hanya boleh mempunyai **satu sesi login aktif**. Login dari browser/perangkat lain saat sesi masih aktif ditolak. Sesi yang tidak aktif akan berakhir otomatis setelah masa lease habis (default 3 menit tanpa heartbeat).
- Browser menyimpan identitas device untuk membantu identifikasi perangkat. Penolakan akses tetap berdasarkan satu sesi aktif per akun.
- Dukungan **Ngrok** disediakan melalui `START_NGROK.bat` agar aplikasi dapat dibuka dari jaringan/internet melalui URL HTTPS Ngrok.
- Jenis Lomba tetap menjadi master dinamis. Admin dapat menambah, mengedit, dan menghapus jenis lomba beserta kriteria penilaiannya.
- Total bobot kriteria setiap lomba wajib **tepat 100 poin**.
- Input nilai mendukung **2 angka di belakang koma**.
- Backup/restore tetap menggunakan database SQLite dan mencakup data peserta, nilai, user, penugasan juri, jenis lomba, kriteria, dan pengaturan.

## Menjalankan di komputer lokal
1. Pastikan **Node.js 20+** terpasang.
2. Jalankan `START_SERVER.bat`.
3. Buka `http://localhost:3000`.
4. Login Admin default: `admin` / `admin123`.

## Menjalankan dengan Ngrok
1. Install **ngrok** dan pastikan perintah `ngrok version` dapat dijalankan dari CMD.
2. Login/configure token Ngrok sekali pada komputer tersebut menggunakan perintah resmi Ngrok.
3. Jalankan `START_NGROK.bat`.
4. Jendela Ngrok akan menampilkan URL HTTPS publik, misalnya `https://xxxxx.ngrok-free.app`.
5. Buka URL tersebut pada HP/laptop yang akan digunakan.
6. Semua perangkat yang membuka URL yang sama akan menggunakan **database server yang sama** di komputer yang menjalankan aplikasi.

### Catatan Ngrok
- Jangan menutup jendela server atau jendela Ngrok selama kegiatan berlangsung.
- Untuk keamanan kegiatan, gunakan password akun yang tidak mudah ditebak dan jangan membagikan akses Admin.
- Jika server dipindahkan ke port lain, sesuaikan nilai `PORT` pada `START_NGROK.bat`.

## Struktur data tingkat
Empat tingkat yang tersedia secara bawaan:
- `TEMU_MINGGU` — Temu Minggu
- `SEKAMI` — Sekami
- `TARUK` — Taruk
- `OMK` — OMK

Contoh penugasan: Juri 1 dapat menilai **Lagu dan Gerak – Temu Minggu, Sekami**, sementara Juri 2 menilai **Lagu dan Gerak – Taruk, OMK**. Hasil ranking setiap tingkat tetap berdiri sendiri.

## Data lama V7
Saat database lama pertama kali dibuka, peserta lama akan dianggap berada pada tingkat **Temu Minggu**. Penugasan juri lama dimigrasikan agar tetap memiliki penugasan pada empat tingkat, sehingga akun lama tidak langsung kehilangan akses.
## Deploy gratis ke Render

Paket ini sudah disiapkan agar dapat dideploy sebagai Web Service Node.js.

1. Upload seluruh folder ke GitHub repository.
2. Di Render pilih **New + → Web Service**, lalu hubungkan repository tersebut.
3. Gunakan pengaturan:
   - Runtime: **Node**
   - Build Command: `npm install`
   - Start Command: `npm start`
   - Health Check Path: `/api/health`
4. Tambahkan Environment Variable:
   - `NODE_ENV=production`
   - `SESSION_SECRET=` isi dengan teks acak yang panjang
5. Deploy lalu buka URL yang diberikan Render.

### Catatan penting tentang database

Versi aplikasi ini memakai **SQLite** pada folder `server/database.sqlite`. Hosting gratis dengan filesystem ephemeral dapat menghapus perubahan database ketika service restart, redeploy, atau dipindahkan. Karena e-Juri menyimpan peserta, user, nilai, ranking, dan pengaturan di SQLite, gunakan fitur **Backup Database** secara berkala. Untuk penggunaan lomba yang membutuhkan penyimpanan permanen di cloud, langkah berikutnya adalah memindahkan database ke PostgreSQL/layanan database persisten atau menggunakan hosting dengan persistent disk.

### Pengujian setelah online

- Buka `/api/health` dan pastikan mendapat JSON `ok: true`.
- Login Admin: `admin` / `admin123`.
- Segera ganti password Admin dari menu **Juri & User**.
- Buat akun Juri dan atur lomba + tingkatnya.
- Uji input nilai, hasil/ranking, export, dan `/live`.

