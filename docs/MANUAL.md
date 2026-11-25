# Manual Pengguna MOPS
## 1. Tentang Aplikasi
**MOPS (Megay Operations Performance System)** adalah aplikasi web untuk mencatat dan memantau operasional pabrik: penerimaan material gudang, produksi, energi (listrik, air, BBM), maintenance, dan kegiatan harian, lengkap dengan dashboard KPI, grafik, dan laporan.
- Data disimpan di Google Sheets milik perusahaan; tampilan berjalan di browser lewat GitHub Pages.
- Login memakai akun Google. Hanya email yang didaftarkan Super Admin yang dapat masuk.
- Setiap data melewati alur persetujuan dan tercatat di log audit.
## 2. Mengakses & Mengunduh
1. Buka alamat aplikasi dari Super Admin (bentuk `https://nama.github.io/mops/`) di Chrome, Edge, atau Safari versi terbaru.
2. Tidak ada yang perlu diinstal. Agar mudah dibuka: di HP pilih menu browser → **Tambahkan ke layar utama**; di komputer pilih menu Chrome → **Simpan dan bagikan → Buat pintasan**.
3. **Mengunduh data:** menu **Reports** → pilih jenis dan periode → tombol **Excel** atau **PDF**. Setiap tabel data punya tombol **CSV** dan **Excel**.
4. Di HP/Android, ketuk tombol **☰** di kiri atas untuk membuka menu. Data tampil sebagai kartu yang mudah digulir; kolom lengkap bisa dilihat saat **Edit**, di Excel, atau di laptop.
## 3. Pengaturan Awal (Super Admin)
Lakukan berurutan, sekali saja:
1. **Personalisasi** (menu Personalisasi, hanya Admin/Super Admin): isi nama perusahaan, unggah logo, pilih tema warna → **Simpan**. Berlaku untuk semua pengguna.
2. **Master Data** sebelum ada transaksi: Department (ID persis `WH`, `PROD`, `EN`, `MT`), Shift, Material, Product, Equipment, Warehouse, UOM.
3. **Tarif** di MASTER TARIFF: Type persis `ELECTRICITY_WBP`, `ELECTRICITY_LWBP`, `WATER`, `FUEL_<JENIS>` (contoh `FUEL_SOLAR`), isi EffectiveDate dan Value.
4. **Target KPI** di KPI TARGET: KpiName persis seperti nama KPI di dashboard, Target, WarningThreshold, Direction (HIGHER bila makin tinggi makin baik, LOWER bila makin rendah makin baik), EffectiveDate.
5. **Pengguna** di User Management (lihat bagian 4).
### Personalisasi
| Pengaturan | Keterangan |
|---|---|
| Nama Perusahaan | Tampil di halaman login, menu, judul tab, dan header PDF |
| Logo | PNG/JPG/SVG sederhana; otomatis dikecilkan (maks ±300 px). Logo yang terlalu rumit akan ditolak |
| Warna utama | Warna menu dan tombol. Pilih warna **gelap** agar tulisan putih terbaca |
| Warna aksen | Garis judul tabel dan aksen |
Tombol **Reset default** mengembalikan tampilan awal (klik **Simpan** untuk menerapkannya ke semua pengguna).
## 4. Pengguna & Pengaturan Akses
### Menambah pengguna
1. Menu User Management → **+ Baru**.
2. Isi UserID (misal `USR-002`), **Email** (alamat Google persis), Name, Role, DepartmentID → **Simpan**.
3. Jika aplikasi Google masih berstatus Testing, daftarkan juga emailnya di Google Cloud → OAuth consent screen → Test users.
4. Nonaktifkan pengguna dengan mengubah Status menjadi `INACTIVE` (jangan hapus agar riwayat tetap utuh).
### Peran
| Peran | Hak akses |
|---|---|
| SUPER_ADMIN | Semua, termasuk user, CLOSE, dan koreksi |
| ADMIN | Input, master data, review, approve, lihat log |
| SUPERVISOR | Input, REVIEW, APPROVE di departemennya |
| OPERATOR | Input dan edit DRAFT milik sendiri, SUBMIT |
| VIEWER | Hanya melihat |
### DepartmentID
Untuk SUPERVISOR dan OPERATOR isi kode modul `WH`, `PROD`, `EN`, `MT` (boleh beberapa dipisah koma) atau `ALL`. **Jika kosong, pengguna tidak dapat menulis data.**
## 5. Penggunaan Awal
1. Login → Dashboard tampil; nilai **n/a** normal sebelum ada data.
2. Buka modul sesuai tugas → **+ Baru** → isi form (tanda * wajib; kolom "otomatis" dihitung sistem) → **Simpan Draft**.
3. Periksa data, lalu klik **SUBMITTED**. Supervisor melakukan **REVIEWED** dan **APPROVED**; Super Admin melakukan **CLOSED**.
4. Setelah SUBMITTED, data terkunci. Perbaikan dilakukan Super Admin lewat **Koreksi** (kembali ke DRAFT dengan alasan).
5. Tunggu pesan hijau "Berhasil". Jika muncul "belum terkonfirmasi", **cek daftar dahulu sebelum input ulang**.
Alur status: `DRAFT → SUBMITTED → REVIEWED → APPROVED → CLOSED`.
### Panduan modul
| Modul | Hal penting |
|---|---|
| Warehouse Receiving | Isi Gross dan Tare; Net dan Accepted otomatis. Tombol **Stok** menampilkan stok per gudang dan material |
| Warehouse Movement | RECEIPT, ISSUE, TRANSFER, ADJUSTMENT. Transfer memindahkan stok antar gudang |
| Production | Satu header per tanggal, shift, dan line (tanggal = hari **mulai** shift). Output dicatat di PROD OUTPUT per kategori GOOD/SIDE_PRODUCT/REJECT; satu kg tidak boleh masuk dua kategori |
| Energy | Listrik WBP dan LWBP terpisah; air dengan DataBasis (MEASURED/ALLOCATED/ESTIMATED); BBM = stok awal + terima − stok akhir. Kosongkan tarif agar terisi otomatis dari MASTER TARIFF |
| Maintenance | Jenis PREVENTIVE/CORRECTIVE/BREAKDOWN; durasi dihitung dari jam, termasuk lewat tengah malam |
| Daily Activity | Pilih DepartmentID Anda; ProgressStatus terpisah dari status approval |
### Dashboard & Laporan
- Dashboard: pilih rentang tanggal → **Terapkan**. Kartu KPI berwarna hijau (sesuai target), kuning (batas peringatan), merah (di luar target), abu-abu (target belum diatur atau data kurang). Ada empat grafik: output dan yield, komposisi output, biaya energi dan listrik, breakdown.
- Hanya data **non-DRAFT** yang dihitung. **n/a berarti data belum cukup, bukan nol.**
- Grafik maksimal 62 hari per tampilan.
- Reports: periode Harian/Mingguan/Bulanan/Custom, ekspor Excel atau PDF.
## 6. Perawatan (Super Admin)
| Tugas | Frekuensi | Cara |
|---|---|---|
| Backup | Harian | Apps Script → Triggers → tambahkan `backupDatabase` (butuh folder Drive `Backups`) |
| Uji pemulihan | Bulanan | Buka salinan backup terbaru dan pastikan data terbaca |
| Tinjau log | Mingguan | Logs → SYSTEM ERROR LOG dan AUDIT LOG |
| Rapikan API_REQUESTS | Bulanan | Salin lalu hapus baris lama (sheet bertambah terus) |
| Perbarui tarif dan target | Saat berubah | Tambah baris baru dengan EffectiveDate baru; jangan menimpa baris lama |
| Tinjau pengguna | Triwulan | Nonaktifkan akun yang tidak lagi bertugas |
| Arsip tahunan | Tahunan | Salin spreadsheet untuk arsip, lalu mulai periode baru bila sheet terlalu besar |
### Memperbarui aplikasi
- **Tampilan:** ganti `index.html` di GitHub (Add file → Upload files). Segarkan browser dengan Ctrl+F5.
- **Backend:** tempel `Code.gs` baru → Simpan → **Terapkan → Kelola deployment → ikon pensil → Versi baru → Terapkan**. URL tidak berubah.
- Simpan salinan `Code.gs`, `index.html`, dan `config.js` yang sedang berjalan sebelum memperbarui.
## 7. Troubleshooting
| Gejala | Penyebab / tindakan |
|---|---|
| Error 400: origin_mismatch | Origin di Google Cloud harus `https://USERNAME.github.io` tanpa `/mops` dan tanpa garis miring akhir; tunggu beberapa menit setelah mengubah |
| Akses ditolak / FORBIDDEN saat login | Email belum terdaftar, Status bukan ACTIVE, atau Anda login dengan akun Google lain |
| Google menolak login (akses diblokir) | Aplikasi masih Testing; tambahkan email di Test users |
| UNAUTHENTICATED | Sesi habis (±1 jam). Muat ulang dan login lagi |
| FORBIDDEN saat menyimpan | Peran atau DepartmentID tidak mencakup modul itu |
| Tarif ... belum ada | Tambah tarif di MASTER TARIFF atau isi manual |
| Duplikat record | Data tanggal/shift/meter yang sama sudah ada |
| Record terkunci | Minta Super Admin melakukan Koreksi |
| Terlalu banyak permintaan | Tunggu 1 menit |
| Status belum terkonfirmasi | Jangan input ulang; periksa daftar, lalu coba lagi bila data belum ada |
| Tampilan tidak berubah setelah update | Ctrl+F5, atau buka jendela penyamaran |
| Logo ditolak | Gunakan gambar lebih sederhana atau lebih kecil |
| Laporan Excel/PDF tidak muncul | Perlu internet (library dimuat online); periksa pemblokir pop-up |
| Panduan tidak muncul | Pastikan file `docs/MANUAL.md` ada di repository |
| Tombol Baru/Edit tidak muncul | Peran Anda tidak punya izin tulis pada tabel itu |
## 8. FAQ
**Apakah data aman?** Data ada di Google Sheets perusahaan. Setiap permintaan diperiksa lewat token Google dan daftar email. Jangan bagikan spreadsheet ke pengguna biasa.
**Boleh dipakai di HP?** Ya, lewat browser. Form panjang lebih nyaman di tablet atau komputer.
**Bisa offline?** Tidak. Aplikasi butuh internet.
**Kenapa angka KPI n/a?** Data pembagi belum ada (misal belum ada input produksi). Itu disengaja agar tidak tampil 0 yang menyesatkan.
**Kenapa data saya tidak masuk KPI?** Masih DRAFT. Hanya status SUBMITTED ke atas yang dihitung.
**Salah input setelah SUBMITTED?** Hubungi Super Admin untuk Koreksi.
**Bisa ganti logo dan warna kapan saja?** Ya, lewat menu Personalisasi oleh Admin atau Super Admin.
**Berapa banyak data yang muat?** Google Sheets maksimal 10 juta sel per spreadsheet; kuota harian Apps Script berlaku. Arsipkan data lama setiap tahun.
**Mengapa MTBF n/a?** Butuh jam operasi per equipment yang belum dicatat di versi ini.
**Lupa password?** Login memakai akun Google; pulihkan lewat Google.
## 9. Praktik Terbaik
- Input di hari yang sama; jangan menunda sampai akhir minggu.
- Satu pengguna, satu akun Google; jangan berbagi akun.
- Lengkapi master data dan tarif sebelum mulai input.
- Pisahkan akun Super Admin dari akun kerja harian bila memungkinkan.
- Jangan membuat repository GitHub berisi data operasional; hanya file aplikasi.
## 10. Istilah
| Istilah | Arti |
|---|---|
| WBP / LWBP | Waktu beban puncak / luar beban puncak (listrik) |
| Yield | Good output dibagi bahan baku masuk |
| Mass Balance | (Good + Side + Reject) dibagi input; idealnya mendekati 100% |
| MTTR | Rata-rata waktu perbaikan breakdown |
| MTBF | Rata-rata waktu antar kerusakan |
| DRAFT | Data belum dikirim, masih bisa diedit |
