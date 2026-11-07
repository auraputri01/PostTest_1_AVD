# Shift Management System (Sistem Informasi Manajemen pada Perusahaan Shift)

============================================================================

## Deskripsi Singkat

Shift adalah perusahaan otomotif yang bergerak di tiga lini bisnis sekaligus: penjualan kendaraan roda dua, pelayanan perawatan dan perbaikan di bengkel resmi, serta penyediaan suku cadang (spare parts). Dengan volume kegiatan harian yang tinggi pada ketiga lini tersebut, perusahaan membutuhkan pengelolaan data yang cepat dan akurat.

Masalah yang dihadapi yaitu Pencatatan data pelanggan, riwayat perbaikan kendaraan, dan keluar-masuk stok suku cadang sering kali masih manual atau tersebar di sistem yang terpisah. Akibatnya data menjadi rangkap dan tidak konsisten, riwayat servis kendaraan mudah hilang, dan jumlah stok fisik suku cadang tidak sama dengan catatan pembukuan saat transaksi servis dilakukan.

Solusinya ialah Data master (pelanggan, kendaraan, karyawan, suku cadang, dan unit kendaraan) digabungkan dengan modul transaksi layanan servis dan penjualan dalam satu sistem. Data disimpan di MySQL dan diakses melalui JDBC. Setiap transaksi dijalankan dengan mekanisme Transaction Control Language (BEGIN, COMMIT, ROLLBACK), sehingga stok suku cadang atau unit kendaraan berkurang otomatis (auto-deduct) saat transaksi berhasil dan perubahan dibatalkan seluruhnya bila ada proses yang gagal.

Pengguna dan hak akses. Sistem memakai pembagian hak akses berbasis peran (Role-Based Access Control):

- Admin memiliki kewenangan penuh, termasuk melihat dashboard, mengelola data pegawai, dan menghapus data.
- Sales melayani pelanggan, mengelola data kendaraan dan unit, serta memproses penjualan unit dan spare part.
- Mekanik menerima dan mengerjakan servis kendaraan serta memantau dan memperbarui suku cadang yang dipakai.
- Pelanggan adalah aktor eksternal yang datanya dikelola oleh sistem dan dapat memantau riwayat servis kendaraannya.

Cara kerja singkatnya yaitu Pengguna masuk dengan username dan password, lalu sistem mengarahkan ke menu utama sesuai peran. Setiap modul menyediakan operasi tambah, lihat, perbarui, dan hapus sesuai kewenangan peran tersebut. Data diperiksa lebih dulu sebelum disimpan, penghapusan memerlukan konfirmasi, dan data yang masih dipakai tabel lain tidak dapat dihapus. Transaksi penjualan dan servis diakhiri dengan pencetakan nota.

Manfaatnya Data menjadi terpusat dan konsisten, stok suku cadang sesuai dengan kondisi sebenarnya secara real-time, pendaftaran servis dan pencarian riwayat kendaraan lebih cepat, serta pihak manajemen dapat memantau kondisi operasional lewat dashboard.

============================================================================

## Fitur Utama Program

### Fitur umum

- Login dengan username dan password, lalu diarahkan ke menu utama sesuai peran (Admin, Sales, atau Mekanik).
- Antarmuka grafis (GUI) untuk mengoperasikan seluruh fitur.
- Validasi input (nomor HP, harga dan stok, kelengkapan data) dan pesan kesalahan yang jelas.
- Konfirmasi sebelum penghapusan, serta pemeriksaan agar data yang masih dipakai data lain tidak terhapus.
- Pemotongan stok otomatis (auto-deduct) dengan mekanisme COMMIT dan ROLLBACK pada transaksi.
- Pencetakan nota setelah transaksi berhasil.

### Fitur Admin

Dashboard dengan enam informasi: total servis hari ini, pendapatan harian, stok menipis, tren servis 7 hari, lima suku cadang terlaris, dan performa mekanik.
Kelola pelanggan, unit, spare part, dan kendaraan (tambah, lihat, perbarui, hapus).
Kelola pegawai (tambah, lihat, perbarui, hapus) dengan pengecekan username unik dan penentuan peran.
Kelola servis (lihat, perbarui, hapus).
Kelola transaksi (buat transaksi, lihat, ubah status pembayaran, hapus transaksi disertai pengembalian stok).
Satu-satunya peran yang dapat menghapus data.

### Fitur Sales

- Kelola pelanggan, kendaraan, dan unit (tambah, lihat, perbarui).
- Lihat dan perbarui harga atau stok spare part.
- Penjualan unit kendaraan lengkap dengan diskon, metode pembayaran, dan nota.
- Penjualan spare part dengan pemeriksaan stok, diskon, metode pembayaran, dan nota.
- Lihat transaksi dan ubah status pembayaran.

### Fitur Mekanik

- Terima servis: memeriksa kendaraan berdasarkan nomor polisi, lalu mencatat kilometer, keluhan, dan jasa.
- Proses servis: mencatat diagnosis dan jasa, memilih spare part yang dipakai dengan pemeriksaan kecukupan stok, lalu mengubah status servis menjadi dikerjakan atau selesai.
- Lihat data servis.
- Lihat dan perbarui harga atau stok spare part.
- Lihat transaksi dan ubah status pembayaran.

============================================================================

## Tech Stack

- Bahasa: Java 21
- Database: MySQL / MariaDB
- Framework: tidak memakai framework (Java murni dengan pola Model, DAO, dan Controller)
- Library: mysql-connector-j 8.3.0 (driver JDBC), java.sql (JDBC), (isi library GUI yang dipakai, contoh: Java Swing atau JavaFX)
- Build tool: Apache Maven
- Perancangan diagram: draw.io (flowchart dan use case)
- IDE: NetBeans

============================================================================

## Perancangan Sistem

Perancangan sistem terdiri dari use case (siapa pengguna dan apa yang dapat dilakukan), flowchart (alur setiap menu), dan ERD (struktur database). Seluruh gambar disimpan di folder docs/diagram/.

### Use Case

<img width="1069" height="1385" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/0fe806e8-7d23-428f-9901-4af3835775c1" />


Sistem memiliki empat aktor dengan batasan hak akses berikut:

- Sales/Kasir melayani transaksi harian, mendaftarkan pelanggan dan kendaraan, serta mengelola stok unit dan suku cadang. Sales memiliki hak Create, Read, dan Update, tetapi tidak memiliki hak Delete.
- Mekanik menangani perbaikan kendaraan. Mekanik dapat membaca dan memperbarui suku cadang, serta mengakses alur transaksi servis.
- Pelanggan adalah aktor eksternal yang dapat melakukan login, memperbarui data pribadi, dan melihat transaksi.
- Admin adalah superuser dengan otorisasi penuh. Hanya Admin yang mengelola data pegawai dan yang dapat menghapus data (kendaraan, pelanggan, stok unit dan tarif, spare part, pegawai, dan transaksi).

Pengelolaan data dipisah per entitas tanpa digabung. Data pelanggan dan data kendaraan adalah dua entitas terpisah dengan operasi masing-masing, data pegawai diatur khusus oleh Admin melalui use case tersendiri, dan tarif jasa terintegrasi langsung pada data stok unit dan suku cadang.

Pada use case Create Transaksi terdapat relasi <<include>> ke empat use case acuan yang wajib dibaca sistem: Read Data Pelanggan, Read Stok Unit & Tarif, Read Spare Part, dan Read Data Kendaraan. Transaksi penjualan dan transaksi servis juga selalu diikuti pencetakan nota.

### Flowchart

Flowchart dikelompokkan menurut peran pengguna. Pada setiap flowchart, bentuk elips menandakan titik awal atau tujuan akhir, belah ketupat adalah pengambilan keputusan, jajar genjang adalah input atau output, dan silinder adalah proses ke database.

### Flowchart Login

### Login

<img width="355" height="1002" alt="Shift-Login drawio" src="https://github.com/user-attachments/assets/b5a65360-e15f-4d63-81cb-d49b9c210f68" />

Proses dimulai dari pilihan login. Pengguna memasukkan username dan password, lalu sistem memeriksa akun. Jika akun salah, pengguna diminta mengisi ulang. Jika benar, sistem memeriksa hak akses (Admin, Sales, atau Mekanik) dan mengarahkan pengguna ke menu utama sesuai perannya.

### Flowchart Admin

**Menu Utama (Admin)**

<img width="2030" height="773" alt="Shift-Menu Utama Admin drawio" src="https://github.com/user-attachments/assets/1fcd68d7-9dcf-4288-91d9-b4f937eadb39" />

Admin melihat delapan menu: kelola pelanggan, kelola unit, kelola suku cadang, kelola kendaraan, dashboard, kelola pegawai, kelola servis, dan kelola transaksi, serta logout. Sistem memeriksa pilihan dan membuka halaman yang sesuai. Pilihan yang tidak ada menampilkan pesan "Pilihan tidak valid".

**Kelola Pelanggan (Admin)**

<img width="1550" height="1574" alt="Shift-Kelola Pelanggan drawio (2)" src="https://github.com/user-attachments/assets/3d4f26ef-6489-4476-ac69-f2572a1cc78e" />

Menu menyediakan tambah, lihat, perbarui, dan hapus. Saat menambah, Admin mengisi nama dan nomor HP, lalu sistem memvalidasi nomor HP sebelum menyimpan. Saat melihat data, sistem memeriksa apakah data ada. Perubahan dilakukan dengan memilih ID pelanggan dan mengisi nomor HP baru. Penghapusan memerlukan konfirmasi dan sistem memeriksa apakah data masih dipakai data lain.

**Kelola Unit (Admin)**

<img width="1378" height="1574" alt="Shift-Kelola Unit drawio (4)" src="https://github.com/user-attachments/assets/b4c82b25-fef5-447f-b46b-ac6809d1de70" />

Admin menambah unit dengan mengisi tipe dan warna, nomor rangka dan nomor mesin, tahun, harga, dan status unit. Data divalidasi sebelum disimpan. Untuk perubahan, Admin memilih ID unit lalu mengisi harga dan status baru. Penghapusan dilakukan setelah konfirmasi.

**Kelola Spare Part (Admin)**

<img width="1378" height="1574" alt="Shift-Kelola Spare Part drawio (7)" src="https://github.com/user-attachments/assets/0a54eb44-4485-4d04-a129-e648f609e859" />

Admin menambah spare part dengan mengisi nama, harga, satuan, dan stok. Harga dan stok divalidasi. Perubahan dilakukan dengan memilih ID spare part lalu mengisi harga atau stok baru. Penghapusan dilakukan setelah konfirmasi.

**Kelola Kendaraan (Admin)**

<img width="1378" height="1574" alt="Shift-Kelola Kendaraan drawio (2)" src="https://github.com/user-attachments/assets/d463e186-2f4d-4ddf-8c60-a086df3f4c51" />

Data yang dikelola adalah nomor polisi, tipe, nomor rangka, nomor mesin, tahun, dan ID pelanggan pemilik. Data divalidasi sebelum disimpan. Admin dapat melihat, memperbarui, dan menghapus kendaraan (dengan konfirmasi).

**Dashboard (Admin)**

<img width="1242" height="2342" alt="Shift-Dashboard drawio (3)" src="https://github.com/user-attachments/assets/b130a9f3-8100-4583-be7f-90fdb202c967" />

Dashboard menyajikan enam informasi: total servis hari ini, pendapatan harian, stok menipis, tren servis tujuh hari, lima suku cadang terlaris, dan performa mekanik. Untuk setiap pilihan, sistem mengambil data dari database, lalu menampilkannya sebagai angka, daftar, atau grafik. Jika data kosong, sistem menampilkan pesan bahwa data belum tersedia.

**Kelola Servis (Admin)**

<img width="1378" height="1344" alt="Shift-Kelola Servis drawio (4)" src="https://github.com/user-attachments/assets/f24d2da2-6f8c-45be-874c-3b3f3c222c25" />

Menu servis untuk Admin terdiri dari lihat, perbarui, dan hapus. Pada pembaruan, Admin memilih ID servis lalu mengisi diagnosis, jasa, dan status. Penghapusan memerlukan konfirmasi.

**Kelola Pegawai**

<img width="1378" height="1574" alt="Shift-Kelola Pegawai drawio (4)" src="https://github.com/user-attachments/assets/02cb9b59-b856-42f3-bdfb-2599546c59a9" />

Admin mengelola nama, nomor HP, alamat, username, password, dan peran pegawai. Saat menambah pegawai, sistem memeriksa bahwa username belum dipakai. Admin juga dapat melihat, memperbarui, dan menghapus data pegawai.

**Kelola Transaksi (Admin)**

<img width="1378" height="1764" alt="Shift-Kelola Transaksi drawio (5)" src="https://github.com/user-attachments/assets/ee59f941-3bcd-4da0-994a-b45ee70b54ed" />

Admin dapat membuat transaksi, melihat transaksi, mengubah status pembayaran, dan menghapus transaksi. Saat membuat transaksi, sistem membedakan transaksi unit dan transaksi spare part atau servis. Admin mengisi diskon dan metode pembayaran, sistem memvalidasi pembayaran, menyimpan data, lalu menampilkan nota. Penghapusan transaksi disertai pengembalian stok.

**Flowchart Mekanik**

**Menu Utama (Mekanik)**

<img width="1104" height="688" alt="Shift-Menu Utama Mekanik drawio (3)" src="https://github.com/user-attachments/assets/4608c0b3-d946-492e-a3b3-d2e845fe53c2" />

Mekanik memiliki tiga menu: kelola servis, kelola spare part, dan lihat transaksi, serta logout. Pilihan yang tidak sesuai menampilkan pesan kesalahan, dan logout mengembalikan pengguna ke halaman login.

**Kelola Servis (Mekanik)**

<img width="1666" height="1197" alt="Shift-Kelola Servis drawio (5)" src="https://github.com/user-attachments/assets/e42fb970-0aa7-48ed-91b1-6de3cd5b2867" />

Menu terdiri dari terima servis, lihat data servis, dan proses servis. Saat menerima servis, Mekanik memasukkan nomor polisi. Jika kendaraan terdaftar, Mekanik mengisi kilometer, keluhan, nama jasa, dan harga jasa, lalu data disimpan dengan status aktif. Jika belum terdaftar, sistem meminta pengguna menghubungi Sales atau Admin. Saat memproses servis, Mekanik memilih servis aktif, mengisi diagnosis, jasa, dan harga, lalu memasukkan spare part dan jumlah yang dipakai. Sistem memeriksa kecukupan stok, dan status servis disimpan sebagai dikerjakan atau selesai.

**Kelola Spare Part (Mekanik)**

<img width="1378" height="975" alt="Shift-Kelola Spare Part drawio (8)" src="https://github.com/user-attachments/assets/c9765ddf-c470-46a6-a972-f790dedd5926" />

Mekanik dapat melihat data spare part dan memperbarui harga atau stok dengan memilih ID spare part yang valid.

**Kelola Transaksi (Mekanik)**

<img width="1871" height="1152" alt="Shift-Kelola Transaksi  drawio" src="https://github.com/user-attachments/assets/9e14e444-d8be-4b3e-bf56-162d0155f0bc" />

Mekanik dapat melihat daftar transaksi dan mengubah status pembayaran dengan memilih ID transaksi lalu status barunya.

**Flowchart Sales**

**Menu Utama (Sales)**

<img width="1862" height="756" alt="Shift-Menu Utama Sales drawio (3)" src="https://github.com/user-attachments/assets/fdc2a49a-b780-4b26-a34d-342b97d83ef0" />

Sales memiliki tujuh menu: kelola pelanggan, kelola kendaraan, kelola unit, kelola spare part, penjualan unit, penjualan spare part, dan kelola transaksi, serta logout.

**Kelola Pelanggan (Sales)**

<img width="1378" height="1202" alt="Shift-Kelola Pelanggan drawio (3)" src="https://github.com/user-attachments/assets/017dde39-bfd7-4c79-a29e-b74bcf5c2a67" />

Sales dapat menambah pelanggan (nama dan nomor HP yang divalidasi), melihat data, dan memperbarui nomor HP berdasarkan ID pelanggan. Sales tidak dapat menghapus data.

**Kelola Kendaraan (Sales)**

<img width="1848" height="1435" alt="Shift-Kelola Kendaraan drawio (3)" src="https://github.com/user-attachments/assets/32cda8d7-dfe2-4dc8-ad53-7e99d8edda5d" />

Sales menambah kendaraan pelanggan (nomor polisi, tipe, nomor rangka, nomor mesin, tahun, dan ID pelanggan) dengan validasi, melihat daftar kendaraan, dan memperbarui data kendaraan.

**Kelola Unit (Sales)**

<img width="1378" height="1202" alt="Shift-Kelola Unit drawio (5)" src="https://github.com/user-attachments/assets/5a75f870-32d3-4a1b-85a2-4a1233f48c83" />

Sales menambah unit dengan mengisi tipe, warna, nomor rangka, nomor mesin, tahun, harga, dan status. Sales juga dapat melihat unit dan memperbarui harga atau status.

**Kelola Spare Part (Sales)**

<img width="1780" height="1224" alt="Shift-Kelola Spare Part drawio (9)" src="https://github.com/user-attachments/assets/77c52de1-c54a-45c7-a426-ab76bd690391" />

Menu ini menyediakan dua pilihan, yaitu melihat data spare part dan memperbarui data spare part. Pada proses melihat data, sistem memeriksa data yang tersimpan dan menampilkannya, atau menampilkan pesan bahwa data belum ada. Pada proses pembaruan, Sales memilih ID spare part, sistem memeriksa validitasnya, lalu Sales mengisi harga atau stok baru yang kemudian disimpan ke database.

**Penjualan Unit**

<img width="1766" height="402" alt="Shift-Penjualan Unit drawio (2)" src="https://github.com/user-attachments/assets/da53dfb2-cd59-4866-8642-b64faeedfcf0" />

Sales memilih pelanggan dan unit, lalu sistem memeriksa ketersediaan unit. Jika tersedia, Sales mengisi diskon dan metode pembayaran, dan sistem memvalidasi pembayaran. Jika valid, sistem menjalankan BEGIN, menyimpan transaksi, dan memperbarui stok unit secara otomatis. Bila seluruh proses berhasil, sistem melakukan COMMIT dan mencetak nota. Bila gagal, sistem melakukan ROLLBACK dan menampilkan pesan bahwa transaksi dibatalkan.

**Penjualan Spare Part**

<img width="1766" height="402" alt="Shift-Penjualan Spare Part drawio (1)" src="https://github.com/user-attachments/assets/da6f8047-07f1-469b-94ff-546121f9881d" />

Sales memilih pelanggan, spare part, dan jumlah. Sistem memeriksa stok, lalu Sales mengisi diskon dan metode pembayaran. Setelah pembayaran valid, transaksi disimpan dan stok spare part dipotong otomatis dalam satu transaksi database. Hasilnya COMMIT disertai nota bila berhasil, atau ROLLBACK bila gagal.

### Kelola Transaksi (Sales)

<img width="1378" height="975" alt="Shift-Kelola Transaksi drawio (6)" src="https://github.com/user-attachments/assets/b3861037-5475-41cd-92b0-1e22130e696e" />

Sales dapat melihat transaksi dan mengubah status pembayaran dengan memilih ID transaksi yang valid dan status barunya.

============================================================================

## ERD

### ERD lOGICAL

<img width="1056" height="733" alt="Logicalnew" src="https://github.com/user-attachments/assets/de089a4a-b259-406c-9bec-4bc5939da969" />

### ERD Relational

<img width="1056" height="733" alt="Logicalnew" src="https://github.com/user-attachments/assets/6c7803e1-0ae1-4283-837c-92dea3d6cc13" />

Database terdiri dari 12 tabel: pelanggan, kendaraan, karyawan, admin, mekanik, sales, servis, jasa_servis, detail_jasa_servis, sparepart, transaksi, dan unit_kendaraan. ERD Logical menunjukkan entitas dan hubungannya, sedangkan ERD Relational sudah memuat Primary Key (PK) dan Foreign Key (FK) pada setiap tabel.

Tabel karyawan adalah supertype yang menyimpan data umum pegawai dan akun login, sedangkan admin, mekanik, dan sales adalah subtype yang menyimpan data khusus tiap peran.

Relasi antar tabel:

1. Pelanggan – Kendaraan 

Setiap Pelanggan dapat memiliki satu atau banyak Kendaraan. 

Setiap Kendaraan harus dimiliki oleh satu dan hanya satu Pelanggan. 

2. Kendaraan – Servis 

Setiap Kendaraan dapat melakukan satu atau banyak Servis. 

Setiap Servis harus dilakukan pada satu dan hanya satu Kendaraan. 

3. Pelanggan – Transaksi 

Setiap Pelanggan dapat melakukan satu atau banyak Transaksi. 

Setiap Transaksi harus dilakukan oleh satu dan hanya satu Pelanggan. 

4. Unit Kendaraan – Transaksi 

Setiap Unit Kendaraan dapat dijual melalui satu Transaksi. 

Setiap Transaksi dapat menjual satu atau banyak Unit Kendaraan. 

5. Sparepart – Transaksi 

Setiap Sparepart dapat dijual melalui satu atau banyak Transaksi. 

Setiap Transaksi dapat menjual satu atau banyak Sparepart. 

6. Sparepart – Servis 

Setiap Sparepart dapat digunakan dalam satu atau banyak Servis. 

Setiap Servis dapat menggunakan satu atau banyak Sparepart. 

7. Transaksi – Servis 

Setiap Transaksi dapat memiliki satu atau banyak Servis. 

Setiap Servis dapat tercatat dalam satu Transaksi. 

8. Servis – Detail Jasa 

Setiap Servis harus memiliki satu atau banyak Detail Jasa. 

Setiap Detail Jasa harus dimiliki oleh satu dan hanya satu Servis. 

9. Jasa Servis – Detail Jasa 

Setiap Jasa Servis dapat dimiliki oleh satu atau banyak Detail Jasa. 

Setiap Detail Jasa harus menggunakan satu dan hanya satu Jasa Servis. 

10. Servis – Karyawan (Mekanik) 

Setiap Servis harus ditangani oleh satu dan hanya satu Mekanik. 

Setiap Mekanik dapat menangani satu atau banyak Servis. 

11. Transaksi – Karyawan (Sales) 

Setiap Transaksi harus ditangani oleh satu dan hanya satu Sales. 

Setiap Sales dapat menangani satu atau banyak Transaksi. 

============================================================================

## Struktur Folder/Package

<img width="517" height="887" alt="image" src="https://github.com/user-attachments/assets/7b89746c-9b5a-4b7a-a95d-9c28f2122e82" />

<img width="512" height="802" alt="image" src="https://github.com/user-attachments/assets/c69c4b42-8af2-4324-ab5e-d3dda1f3e87a" />

***com.bengkelshift.main***

Berisi Main, yaitu titik masuk program. Class ini menjalankan form login melalui SwingUtilities.invokeLater.

***com.bengkelshift.database***

Berisi Koneksi, yang menyediakan koneksi JDBC ke jdbc:mysql://localhost:3306/db_shift2 melalui method Koneksi.getConnection().

***com.bengkelshift.model***

Berisi class data (POJO) yang merepresentasikan tabel di database.

- pengguna: abstract class dengan method abstrak getPeran().
- Karyawan: turunan pengguna, peran "Karyawan".
- Admin: turunan Karyawan, peran "Administrator".
- Mekanik: turunan Karyawan, peran "Mekanik".
- Sales: turunan Karyawan, peran "Sales".
- Pelanggan: data pelanggan (id, nama, no HP).
- Kendaraan: data kendaraan milik pelanggan.
- UnitKendaraan: data unit kendaraan.
- Servis: data servis.
- Sparepart: data sparepart.
- JasaServis: data jenis jasa servis beserta tarifnya.
- DetailJasa: rincian jasa pada suatu servis.
- Transaksi: data transaksi atau pembayaran.

Hierarki pewarisan: pengguna (abstract class) diwarisi oleh Karyawan. Selanjutnya Karyawan diwarisi oleh tiga class, yaitu Admin, Mekanik, dan Sales. Setiap subclass mengimplementasikan getPeran() dengan nilainya sendiri.

***com.bengkelshift.dao***

Data Access Object, yaitu satu-satunya lapisan yang menjalankan query SQL (menggunakan PreparedStatement). Setiap DAO menyediakan operasi CRUD untuk entitasnya.

Daftar class: AdminDAO, DetailJasaDAO, JasaServisDAO, KaryawanDAO, KendaraanDAO, MekanikDAO, PelangganDAO, SalesDAO, ServisDAO, SparepartDAO, TransaksiDAO, UnitKendaraanDAO.

Contoh pada PelangganDAO: tambahPelanggan, getAllPelanggan, updatePelanggan, dan hapusPelanggan.

***com.bengkelshift.controller***

Menjembatani tampilan dan DAO, serta melakukan validasi input.

{Entitas}Controller: satu controller per entitas (Admin, DetailJasa, JasaServis, Karyawan, Kendaraan, Mekanik, Pelanggan, Sales, Servis, Sparepart, Transaksi, UnitKendaraan). Tugasnya memvalidasi data lalu memanggil DAO.
LoginController: autentikasi karyawan (login) dan pengecekan peran admin (isAdmin).
DashboardController: menampilkan ringkasan jumlah data dari tabel-tabel utama.
MenuController: menu berbasis konsol dengan hak akses berbeda untuk admin dan karyawan non-admin.

Contoh validasi pada PelangganController: nama wajib diisi, serta nomor HP hanya boleh angka dan terdiri dari 10 sampai 13 digit.

***com.bengkel.shift.view***

Berisi seluruh form GUI. Setiap form terdiri dari sepasang file: .java (kode) dan .form (desain visual NetBeans).

- view: berisi form login yang dipakai semua pengguna.
- view.admin: form untuk admin, yaitu pelanggan, kendaraan, pegawai, servs, spart, stok, taridf, dan transaksi.
- view.sales: form untuk sales, yaitu main, pelanggan, kendaraan, servis, sparepart, stok, dan tarif.
- view.mekanik: form untuk mekanik, yaitu servs, spart, tarif, dan transaksi.
- view.pelanggan: form untuk pelanggan, yaitu profil dan riwayatransaksi.

============================================================================

## Penerapan 4 Pilar OOP

### Encapsulation

Seluruh atribut pada class di package model dideklarasikan private dan hanya dapat diakses lewat method getter dan setter yang bersifat public. Contohnya pada Karyawan, atribut idKaryawan, namaKaryawan, noHp, alamat, username, password, dan statusAktif diakses melalui getIdKaryawan(), setNamaKaryawan(), dan seterusnya. Dengan cara ini data objek terlindungi dan hanya berubah melalui jalur yang disediakan. Hal yang sama berlaku untuk Pelanggan, Servis, Sparepart, Transaksi, dan model lainnya. Pada Koneksi, konstruktor dibuat private dan atribut koneksi dibuat private static final sehingga class tidak dapat diinstansiasi dan konfigurasinya tidak dapat diubah dari luar.

### Inheritance

Pewarisan diterapkan pada package model dengan tiga tingkat. Karyawan mewarisi pengguna, lalu Admin, Mekanik, dan Sales mewarisi Karyawan. Atribut dan method umum karyawan (id, nama, no HP, alamat, username, password, status aktif) cukup ditulis sekali di Karyawan. Tiap subclass hanya menambahkan atribut khususnya, misalnya Admin menambahkan tanggalMulaiBertugas, dan memakai super(...) pada konstruktornya untuk meneruskan data ke class induk.

### Polymorphism

Polimorfisme diterapkan dalam dua bentuk:

- Overriding

Method abstrak getPeran() pada pengguna ditimpa dengan anotasi @Override di setiap subclass. Karyawan mengembalikan "Karyawan", Admin mengembalikan "Administrator", Mekanik mengembalikan "Mekanik", dan Sales mengembalikan "Sales". Method yang sama menghasilkan perilaku berbeda tergantung jenis objeknya, sehingga dapat dipanggil lewat referensi bertipe pengguna.

- Overloading

Setiap class model memiliki dua konstruktor dengan nama sama tetapi parameter berbeda, yaitu konstruktor kosong dan konstruktor lengkap. Contohnya Pelanggan() dan Pelanggan(int idPelanggan, String namaPelanggan, String noHp).

### Abstraction

Abstraksi diterapkan pada dua tingkat:

- Abstract class

Pengguna adalah abstract class yang hanya mendeklarasikan method getPeran() tanpa isi. Class ini menetapkan kontrak yang wajib dipenuhi oleh turunannya tanpa menentukan cara kerjanya.

- Pemisahan lapisan

Detail teknis akses database disembunyikan di lapisan DAO dan Koneksi. Controller dan View cukup memanggil method seperti tambahPelanggan() atau getAllPelanggan() tanpa perlu mengetahui query SQL maupun cara membuat koneksi. Dengan demikian setiap lapisan hanya mengetahui apa yang perlu diketahuinya.

============================================================================











































