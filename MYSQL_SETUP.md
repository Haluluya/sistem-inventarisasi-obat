# Setup MySQL untuk Sistem Inventarisasi Obat

## Instalasi MySQL

### Ubuntu/Debian
```bash
# Update package list
sudo apt update

# Install MySQL Server
sudo apt install mysql-server

# Secure MySQL installation
sudo mysql_secure_installation
```

### CentOS/RHEL
```bash
# Install MySQL repository
sudo yum install mysql-server

# Start MySQL service
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Get temporary password
sudo grep 'temporary password' /var/log/mysqld.log

# Secure installation
sudo mysql_secure_installation
```

## Konfigurasi Database

### 1. Login ke MySQL
```bash
sudo mysql -u root -p
```

### 2. Buat Database dan User
```sql
-- Buat database
CREATE DATABASE inventarisasi_obat CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Buat user khusus (opsional, untuk keamanan)
CREATE USER 'obat_user'@'localhost' IDENTIFIED BY 'password_yang_kuat';

-- Berikan privileges
GRANT ALL PRIVILEGES ON inventarisasi_obat.* TO 'obat_user'@'localhost';

-- Refresh privileges
FLUSH PRIVILEGES;

-- Keluar dari MySQL
EXIT;
```

### 3. Konfigurasi .env
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=inventarisasi_obat
DB_USERNAME=obat_user
DB_PASSWORD=password_yang_kuat
```

## Migrasi Database

### Jalankan Migrasi
```bash
# Pastikan di direktori project
cd /path/to/sistem-inventarisasi-obat

# Test koneksi database
php artisan tinker
>>> DB::connection()->getPdo();

# Jalankan migrasi
php artisan migrate:fresh --seed
```

### Struktur Database yang Dibuat

#### Tabel Users
- id (Primary Key)
- name (VARCHAR)
- email (VARCHAR, Unique)
- password (VARCHAR)
- role (ENUM: admin, user)
- timestamps

#### Tabel Unit Distribusi
- id (Primary Key)
- kode_unit (VARCHAR, Unique)
- nama_unit (VARCHAR)
- alamat (TEXT)
- telepon (VARCHAR)
- email (VARCHAR)
- status (ENUM: aktif, nonaktif)
- timestamps

#### Tabel Obat
- id (Primary Key)
- kode_obat (VARCHAR, Unique)
- nama_obat (VARCHAR)
- kategori (VARCHAR)
- deskripsi (TEXT)
- stok (INTEGER)
- satuan (VARCHAR)
- harga (DECIMAL)
- tanggal_kadaluarsa (DATE)
- status (ENUM: tersedia, habis, kadaluarsa)
- foto_kemasan (VARCHAR)
- keterangan (TEXT)
- unit_distribusi_id (Foreign Key)
- timestamps

#### Tabel Log Aktivitas
- id (Primary Key)
- user_id (Foreign Key)
- aktivitas (VARCHAR)
- tabel_terkait (VARCHAR)
- data_lama (JSON)
- data_baru (JSON)
- timestamps

## Data Seeder

Sistem akan otomatis membuat data sample:

### Users
- **Admin**: admin@obat.com / password123
- **User**: user@obat.com / password123

### Unit Distribusi
- Apotek Sehat Sentosa
- Apotek Kimia Farma
- Apotek Guardian

### Obat Sample
- Paracetamol 500mg
- Amoxicillin 500mg
- Ibuprofen 400mg
- OBH Combi Batuk Flu
- Vitamin C 1000mg

## Backup dan Restore

### Backup Database
```bash
# Backup lengkap
mysqldump -u obat_user -p inventarisasi_obat > backup_$(date +%Y%m%d_%H%M%S).sql

# Backup hanya struktur
mysqldump -u obat_user -p --no-data inventarisasi_obat > struktur_db.sql

# Backup hanya data
mysqldump -u obat_user -p --no-create-info inventarisasi_obat > data_db.sql
```

### Restore Database
```bash
# Restore dari backup
mysql -u obat_user -p inventarisasi_obat < backup_file.sql
```

## Troubleshooting

### Error: Access denied for user
```bash
# Reset password MySQL root
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new_password';
FLUSH PRIVILEGES;
EXIT;
```

### Error: Connection refused
```bash
# Check MySQL service status
sudo systemctl status mysql

# Start MySQL service
sudo systemctl start mysql

# Enable auto-start
sudo systemctl enable mysql
```

### Error: Database doesn't exist
```sql
-- Login ke MySQL dan buat database
CREATE DATABASE inventarisasi_obat CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Error: Table doesn't exist
```bash
# Jalankan migrasi ulang
php artisan migrate:fresh --seed
```

## Optimisasi MySQL

### Konfigurasi my.cnf
```ini
[mysqld]
# Basic settings
default-storage-engine = innodb
innodb_buffer_pool_size = 256M
innodb_log_file_size = 64M

# Character set
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

# Connection settings
max_connections = 100
connect_timeout = 60
wait_timeout = 28800

# Query cache
query_cache_type = 1
query_cache_size = 32M
```

### Index Optimization
Database sudah dilengkapi dengan index yang optimal:
- Primary keys pada semua tabel
- Unique index pada email users
- Unique index pada kode_obat dan kode_unit
- Foreign key constraints

## Monitoring

### Check Database Size
```sql
SELECT 
    table_schema AS 'Database',
    ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS 'Size (MB)'
FROM information_schema.tables 
WHERE table_schema = 'inventarisasi_obat'
GROUP BY table_schema;
```

### Check Table Status
```sql
SHOW TABLE STATUS FROM inventarisasi_obat;
```

### Monitor Connections
```sql
SHOW PROCESSLIST;
```

---
**Database**: MySQL 5.7+
**Character Set**: utf8mb4
**Collation**: utf8mb4_unicode_ci
**Engine**: InnoDB

