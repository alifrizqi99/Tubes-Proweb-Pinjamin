# Pinjamin - Sistem Peminjaman Barang

## Tech Stack

### Backend

-   **Laravel 12** - PHP Framework
-   **Laravel Passport** - OAuth2 Authentication
-   **MySQL/SQLite** - Database
-   **Queue System** - Background job processing

### Frontend

-   **Vite** - Build tool & development server
-   **Tailwind CSS 4** - Utility-first CSS framework
-   **DaisyUI** - Tailwind CSS component library
-   **Lucide Icons** - Icon library
-   **Axios** - HTTP client
-   **Blade Templates** - Server-side rendering

### Development Tools

-   **Pest PHP** - Testing framework
-   **Laravel Pint** - Code style fixer
-   **Laravel Sail** - Docker development environment
-   **Concurrently** - Run multiple commands simultaneously

## Requirements

Pastikan sistem Anda memiliki:

-   **PHP** >= 8.2
-   **Composer** >= 2.0
-   **Node.js** >= 18.0
-   **NPM** atau **PNPM**
-   **MySQL** >= 8.0 atau **SQLite** >= 3.0
-   **Git**

## Instalasi

### 1. Clone Repository

```bash
git clone <repository-url>
cd pinjamin-FE
```

### 2. Install Dependencies

#### Menggunakan Composer Script (Recommended)

```bash
composer run setup
```

Script ini akan otomatis:

-   Install PHP dependencies
-   Copy `.env.example` ke `.env`
-   Generate application key
-   Run migrations
-   Install Node.js dependencies
-   Build frontend assets

#### Manual Installation

Jika Anda ingin install secara manual:

```bash
# Install PHP dependencies
composer install

# Copy environment file
cp .env.example .env

# Generate application key
php artisan key:generate

# Install Node.js dependencies
npm install
# atau jika menggunakan pnpm
pnpm install
```

### 3. Konfigurasi Database

Edit file `.env` dan sesuaikan konfigurasi database:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3307
DB_DATABASE=pinjamin_db
DB_USERNAME=root
DB_PASSWORD=
```

Atau gunakan SQLite untuk development:

```env
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/database.sqlite
```

### 4. Setup Database

```bash
# Create database (jika menggunakan SQLite)
touch database/database.sqlite

# Run migrations
php artisan migrate

# (Optional) Seed database dengan data dummy
php artisan db:seed
```

### 5. Setup Laravel Passport

```bash
# Install Passport
php artisan passport:install

# Generate encryption keys
php artisan passport:keys
```

Setelah menjalankan `passport:install`, Anda akan mendapatkan Client ID dan Secret. Simpan informasi ini jika diperlukan untuk API authentication.

### 6. Setup Storage

```bash
# Create symbolic link untuk public storage
php artisan storage:link
```

### 7. Build Frontend Assets

```bash
# Development build
npm run build

# Atau untuk development dengan hot reload
npm run dev
```

## Cara Menjalankan Aplikasi

### Menggunakan Composer Script (Recommended)

Jalankan semua service sekaligus (server, queue, logs, vite):

```bash
composer run dev
```

Script ini akan menjalankan:

-   🌐 Laravel development server (`php artisan serve`) - Port 8000
-   🔄 Queue worker (`php artisan queue:listen`)
-   📋 Log viewer (`php artisan pail`)
-   ⚡ Vite development server - Port 5173

Aplikasi dapat diakses di: **http://localhost:8000**

### Manual

Jika Anda ingin menjalankan secara manual, buka 4 terminal berbeda:

#### Terminal 1 - Laravel Server

```bash
php artisan serve
```

#### Terminal 2 - Queue Worker

```bash
php artisan queue:listen --tries=1
```

#### Terminal 3 - Log Viewer (Optional)

```bash
php artisan pail
```

#### Terminal 4 - Vite Development Server

```bash
npm run dev
```

### Production Build

```bash
# Build assets untuk production
npm run build

# Jalankan dengan web server (Apache/Nginx)
# atau gunakan Laravel Octane untuk performa tinggi
```

## Struktur Database

### Users Table

-   `id` - Primary key
-   `name` - Nama lengkap
-   `email` - Email (unique)
-   `nim` - Nomor Induk Mahasiswa
-   `role` - Role user (`peminjam` atau `costumer`)
-   `password` - Hashed password

### Items Table

-   `id` - Primary key
-   `nama` - Nama barang
-   `stok` - Jumlah stok tersedia
-   `deskripsi` - Deskripsi barang
-   `max_hari` - Maksimal hari peminjaman
-   `gambar` - Path file gambar
-   `user_id` - Foreign key ke users (pemilik)

### Borrowings Table

-   `id` - Primary key
-   `peminjam_id` - Foreign key ke users (peminjam)
-   `item_id` - Foreign key ke items
-   `foto_ktm` - Path foto KTM peminjam
-   `tanggal_pinjam` - Tanggal mulai pinjam
-   `tanggal_kembali` - Tanggal deadline kembali
-   `lama_hari` - Durasi peminjaman (hari)
-   `tanggal_pengembalian_aktual` - Tanggal pengembalian sebenarnya
-   `status` - Status peminjaman:
    -   `pending` - Menunggu persetujuan
    -   `approved` - Disetujui, sedang dipinjam
    -   `rejected` - Ditolak
    -   `returned` - Sudah dikembalikan
    -   `cancelled` - Dibatalkan oleh peminjam
    -   `return_pending` - Menunggu konfirmasi pengembalian
-   `catatan` - Catatan peminjaman
-   `kondisi` - Kondisi barang saat dikembalikan
-   `foto_kondisi` - Foto kondisi barang
-   `rating` - Rating peminjaman
-   `alasan_penolakan` - Alasan jika ditolak

### Notifications Table

-   `id` - Primary key
-   `user_id` - Foreign key ke users
-   `type` - Tipe notifikasi
-   `title` - Judul notifikasi
-   `message` - Pesan notifikasi
-   `read_at` - Timestamp dibaca
-   `data` - JSON data tambahan

## API Endpoints

### Authentication

```
POST /login - Login user
POST /register - Register user baru
POST /logout - Logout user
```

### Items

```
GET  /api/items - List semua barang
GET  /api/items/{id} - Detail barang
GET  /api/items/my-items - Barang milik user
GET  /api/items/search?q={query} - Search barang
POST /api/items - Tambah barang baru (costumer only)
PUT  /api/items/{id} - Update barang (owner only)
DELETE /api/items/{id} - Hapus barang (owner only)
```

### Borrowings (Peminjaman)

```
GET  /api/borrowings - List peminjaman user
GET  /api/borrowings/{id} - Detail peminjaman
GET  /api/borrowings/history - Riwayat peminjaman
POST /api/borrowings - Ajukan peminjaman baru
POST /api/borrowings/{id}/cancel - Cancel peminjaman
POST /api/borrowings/{id}/return - Request pengembalian
POST /api/borrowings/{id}/approve - Approve peminjaman (owner)
POST /api/borrowings/{id}/reject - Reject peminjaman (owner)
POST /api/borrowings/{id}/approve-return - Approve pengembalian (owner)
```

### Notifications

```
GET  /api/notifications - List notifikasi
GET  /api/notifications/unread-count - Jumlah notifikasi belum dibaca
POST /api/notifications/mark-all-read - Tandai semua sebagai dibaca
POST /api/notifications/{id}/mark-read - Tandai satu notifikasi dibaca
DELETE /api/notifications/{id} - Hapus notifikasi
```

### Authentication Header

Untuk API endpoints yang memerlukan autentikasi, sertakan token di header:

```
Authorization: Bearer {access_token}
```

## Struktur Folder

```
pinjamin-FE/
├── app/
│   ├── Http/
│   │   ├── Controllers/      # Controllers untuk handle requests
│   │   └── Middleware/        # Custom middleware
│   ├── Models/                # Eloquent models
│   └── Services/              # Business logic services
├── database/
│   ├── migrations/            # Database migrations
│   └── seeders/               # Database seeders
├── public/
│   └── storage/               # Symlink untuk file uploads
├── resources/
│   ├── css/                   # Stylesheets
│   ├── js/                    # JavaScript files
│   └── views/                 # Blade templates
├── routes/
│   ├── web.php                # Web routes
│   └── api.php                # API routes
├── storage/
│   ├── app/public/            # User uploaded files
│   └── logs/                  # Application logs
└── tests/                     # Test files
```

## Troubleshooting

### Error: "No application encryption key has been specified"

```bash
php artisan key:generate
```

### Error: Storage link tidak bekerja

```bash
php artisan storage:link
```

### Error: Vite manifest not found

```bash
npm run build
```

### Error: Queue jobs tidak berjalan

Pastikan queue worker berjalan:

```bash
php artisan queue:listen
```

### Error: Passport keys tidak ditemukan

```bash
php artisan passport:keys
php artisan passport:install
``
```
