# Sistem Inventarisasi Gudang Obat

Aplikasi web Laravel untuk mengelola inventarisasi obat dan unit distribusi dengan fitur lengkap CRUD, autentikasi role-based, validasi form, upload file, dan export data.

## 🚀 Fitur Utama

- **Sistem Autentikasi** - Login/Register dengan role Admin dan User
- **CRUD Data Obat** - Kelola data obat dengan foto kemasan
- **CRUD Unit Distribusi** - Kelola unit distribusi obat
- **Pencarian & Filter** - Cari dan filter data dengan mudah
- **Export Data** - Export ke PDF dan Excel
- **Dashboard Statistik** - Tampilan statistik real-time
- **Log Aktivitas** - Pencatatan semua aktivitas admin
- **Responsive Design** - Tampilan optimal di desktop dan mobile

## 🛠️ Teknologi

- **Backend:** Laravel 10.x
- **Frontend:** Bootstrap 5 + Alpine.js
- **Database:** SQLite
- **Export:** DomPDF + Laravel Excel

## 📋 Persyaratan Sistem

- PHP 8.1+
- Composer
- SQLite
- Web Server (Apache/Nginx)

## 🔧 Instalasi

1. **Clone Repository**
   ```bash
   git clone <repository-url>
   cd sistem-inventarisasi-obat
   ```

2. **Install Dependencies**
   ```bash
   composer install
   ```

3. **Setup Environment**
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. **Setup Database**
   ```bash
   touch database/database.sqlite
   php artisan migrate
   php artisan db:seed
   ```

5. **Create Storage Link**
   ```bash
   php artisan storage:link
   ```

6. **Run Application**
   ```bash
   php artisan serve
   ```

7. **Akses Aplikasi**
   Buka browser dan akses: `http://localhost:8000`

## 👤 Default Users

Setelah menjalankan seeder, tersedia user default:

**Admin:**
- Email: admin@example.com
- Password: password
- Role: Admin

**User:**
- Email: user@example.com
- Password: password
- Role: User

## 📱 Penggunaan

### Registrasi User Baru
1. Klik "Register" di halaman utama
2. Isi form registrasi lengkap
3. Pilih role (Admin/User)
4. Klik "Register"

### Mengelola Data Obat
1. Login sebagai Admin
2. Masuk ke menu "Data Obat"
3. Klik "Tambah Obat" untuk menambah data baru
4. Isi form lengkap dengan foto kemasan
5. Gunakan fitur pencarian dan filter sesuai kebutuhan

### Export Data
1. Di halaman Data Obat, klik tombol "Export"
2. Pilih format PDF atau Excel
3. File akan otomatis terdownload

## 🔒 Keamanan

- Password hashing dengan bcrypt
- CSRF protection pada semua form
- XSS protection built-in Laravel
- File upload validation
- Role-based access control

## 📊 Database Schema

### Users
- id, name, email, role, password, timestamps

### Obat
- id, kode_obat, nama_obat, kategori, deskripsi
- stok, harga, satuan, tanggal_kadaluarsa
- foto_kemasan, unit_distribusi_id, status, keterangan
- timestamps

### Unit Distribusi
- id, nama_unit, alamat, kontak, email
- status, keterangan, timestamps

### Log Aktivitas
- id, user_id, aktivitas, tabel_terkait, id_terkait
- data_lama, data_baru, ip_address, user_agent
- timestamps

## 🎯 Validasi Form

### Server-side (Laravel FormRequest)
- Validasi tipe data dan format
- Custom error messages
- Unique validation untuk kode obat
- File upload validation

### Client-side (Alpine.js)
- Real-time validation feedback
- Form submission prevention
- User-friendly error display

## 📈 Performance

- Eager loading untuk optimasi query
- Pagination untuk data besar
- Efficient search dengan database indexing
- Optimized asset loading

## 🐛 Troubleshooting

### Error: "Storage link not found"
```bash
php artisan storage:link
```

### Error: "Database not found"
```bash
touch database/database.sqlite
php artisan migrate
```

### Error: "Permission denied"
```bash
chmod -R 775 storage bootstrap/cache
```

## 📝 Lisensi

Aplikasi ini dibuat untuk keperluan pembelajaran dan pengembangan sistem inventarisasi farmasi.

## 👨‍💻 Developer

---

**Catatan:** Pastikan semua persyaratan sistem terpenuhi sebelum instalasi. Untuk production deployment, gunakan web server yang proper dan konfigurasi keamanan yang sesuai.

