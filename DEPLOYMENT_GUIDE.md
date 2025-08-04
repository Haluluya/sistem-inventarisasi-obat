# Panduan Deployment - Sistem Inventarisasi Obat Modern

## Persyaratan Sistem

### Server Requirements
- PHP 8.1 atau lebih tinggi
- Composer
- MySQL 5.7+ atau SQLite 3.8+
- Apache/Nginx web server
- Node.js 16+ (untuk development)

### PHP Extensions
- BCMath
- Ctype
- Fileinfo
- JSON
- Mbstring
- OpenSSL
- PDO
- Tokenizer
- XML
- GD (untuk image processing)

## Langkah-langkah Deployment

### 1. Persiapan Server
```bash
# Update sistem
sudo apt update && sudo apt upgrade -y

# Install PHP dan extensions
sudo apt install php8.1 php8.1-cli php8.1-fpm php8.1-mysql php8.1-xml php8.1-curl php8.1-mbstring php8.1-zip php8.1-gd php8.1-sqlite3

# Install Composer
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

### 2. Deploy Aplikasi
```bash
# Extract file sistem
tar -xzf sistem-inventarisasi-obat-modernized.tar.gz
cd sistem-inventarisasi-obat

# Install dependencies
composer install --optimize-autoloader --no-dev

# Setup environment
cp .env.example .env
php artisan key:generate
```

### 3. Konfigurasi Database

#### Untuk MySQL (Recommended):
```bash
# Install MySQL
sudo apt install mysql-server

# Secure installation
sudo mysql_secure_installation

# Login dan setup database
sudo mysql -u root -p
```

```sql
CREATE DATABASE inventarisasi_obat CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'obat_user'@'localhost' IDENTIFIED BY 'password_yang_kuat';
GRANT ALL PRIVILEGES ON inventarisasi_obat.* TO 'obat_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=inventarisasi_obat
DB_USERNAME=obat_user
DB_PASSWORD=password_yang_kuat
```

#### Untuk SQLite (Development Only):
```env
DB_CONNECTION=sqlite
DB_DATABASE=/path/to/database.sqlite
```

### 4. Setup Database
```bash
# Test koneksi database
php artisan tinker
>>> DB::connection()->getPdo();
>>> exit

# Jalankan migrasi dan seeding
php artisan migrate:fresh --seed
```

### 5. Konfigurasi Web Server

#### Apache (.htaccess)
```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteRule ^(.*)$ public/$1 [L]
</IfModule>
```

#### Nginx
```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /path/to/sistem-inventarisasi-obat/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

### 6. Permissions
```bash
# Set proper permissions
sudo chown -R www-data:www-data /path/to/sistem-inventarisasi-obat
sudo chmod -R 755 /path/to/sistem-inventarisasi-obat
sudo chmod -R 775 /path/to/sistem-inventarisasi-obat/storage
sudo chmod -R 775 /path/to/sistem-inventarisasi-obat/bootstrap/cache
```

### 7. Optimisasi Production
```bash
# Cache konfigurasi
php artisan config:cache

# Cache routes
php artisan route:cache

# Cache views
php artisan view:cache

# Optimize autoloader
composer dump-autoload --optimize
```

## Akun Default

Setelah seeding database, akun berikut tersedia:

### Administrator
- **Email**: admin@obat.com
- **Password**: password123
- **Role**: admin

### User Test
- **Email**: test@test.com  
- **Password**: password
- **Role**: admin

## Troubleshooting

### Error 500 - Internal Server Error
1. Periksa log error: `tail -f storage/logs/laravel.log`
2. Pastikan permissions sudah benar
3. Periksa konfigurasi web server

### Database Connection Error
1. Periksa kredensial database di `.env`
2. Pastikan database service berjalan
3. Test koneksi: `php artisan tinker` → `DB::connection()->getPdo()`

### File Permission Issues
```bash
# Reset permissions
sudo chown -R www-data:www-data .
sudo chmod -R 755 .
sudo chmod -R 775 storage bootstrap/cache
```

### CSS/JS Not Loading
1. Periksa path di web server config
2. Pastikan public folder accessible
3. Clear cache: `php artisan cache:clear`

## Maintenance

### Update Sistem
```bash
# Backup database
php artisan backup:run

# Update dependencies
composer update

# Run migrations
php artisan migrate

# Clear caches
php artisan cache:clear
php artisan config:clear
php artisan view:clear
```

### Monitoring
- Monitor log files di `storage/logs/`
- Setup log rotation
- Monitor disk space untuk uploads
- Backup database secara berkala

## Security Checklist

- [ ] Update `.env` dengan kredensial production
- [ ] Set `APP_DEBUG=false` di production
- [ ] Setup HTTPS/SSL certificate
- [ ] Configure firewall rules
- [ ] Regular security updates
- [ ] Backup strategy implemented

## Support

Untuk bantuan teknis atau pertanyaan deployment, silakan hubungi tim development atau buat issue di repository project.

---
**Last Updated**: August 4, 2025
**Version**: 2.0.0

