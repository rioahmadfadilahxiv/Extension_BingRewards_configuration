# Remote Kill Switch Setup Guide

## Overview

Fitur Remote Kill Switch memungkinkan Anda untuk mengaktifkan atau menonaktifkan extension dari jarak jauh menggunakan file JSON yang di-host di GitHub.

## Prerequisites

- Akun GitHub (gratis)
- Extension sudah terinstall

## Setup Steps

### 1. Buat GitHub Repository

1. Login ke [GitHub](https://github.com)
2. Klik tombol **"New"** untuk membuat repository baru
3. Beri nama repository (contoh: `extension-status`)
4. Pilih **Public** atau **Private** (keduanya bisa digunakan)
5. Klik **"Create repository"**

### 2. Upload File Status

1. Di repository yang baru dibuat, klik **"Add file"** → **"Create new file"**
2. Beri nama file: `status.json`
3. Copy paste kode berikut:

```json
{
  "enabled": true,
  "message": "Extension is active",
  "lastUpdate": "2026-02-16T22:00:00Z"
}
```

4. Klik **"Commit new file"**

### 3. Dapatkan Raw URL

1. Buka file `status.json` yang baru dibuat
2. Klik tombol **"Raw"** di kanan atas
3. Copy URL dari address bar browser Anda
4. URL akan berbentuk: `https://raw.githubusercontent.com/USERNAME/REPO/main/status.json`

### 4. Update Extension Configuration

1. Buka file `js/background.js` di folder extension Anda
2. Cari baris yang berisi `KILL_SWITCH_URL`
3. Ganti URL dengan URL raw yang Anda copy tadi:

```javascript
const KILL_SWITCH_URL = "https://raw.githubusercontent.com/USERNAME/REPO/main/status.json";
```

4. Save file

### 5. Reload Extension

1. Buka `chrome://extensions/`
2. Klik tombol **reload** (icon refresh) pada extension Anda
3. Extension sekarang sudah terhubung dengan kill switch

## Usage

### Menonaktifkan Extension

1. Buka file `status.json` di GitHub repository Anda
2. Klik tombol **Edit** (icon pensil)
3. Ubah `"enabled": true` menjadi `"enabled": false`
4. Ubah `"message"` sesuai keinginan (akan ditampilkan ke user)
5. Update `"lastUpdate"` dengan timestamp saat ini
6. Klik **"Commit changes"**

Contoh:
```json
{
  "enabled": false,
  "message": "Extension is temporarily disabled for maintenance. Please check back later.",
  "lastUpdate": "2026-02-16T23:00:00Z"
}
```

**Hasil:** Extension akan berhenti bekerja dalam waktu maksimal 60 menit (atau saat user restart extension)

### Mengaktifkan Kembali Extension

1. Buka file `status.json` di GitHub
2. Klik **Edit**
3. Ubah `"enabled": false` menjadi `"enabled": true`
4. Update `"message"` dan `"lastUpdate"`
5. Klik **"Commit changes"**

**Hasil:** Extension akan aktif kembali dalam waktu maksimal 60 menit (atau saat user restart extension)

## How It Works

1. **Initial Check**: Extension mengecek status saat pertama kali diload
2. **Periodic Check**: Extension mengecek status setiap 60 menit
3. **Enforcement**: Jika `enabled: false`, semua fitur extension akan dinonaktifkan
4. **UI Update**: Popup akan menampilkan overlay dengan pesan disabled
5. **Fail-Safe**: Jika GitHub tidak bisa diakses, extension tetap berjalan normal

## Troubleshooting

### Extension tidak merespon perubahan status

**Solusi:**
1. Pastikan URL raw sudah benar
2. Pastikan file `status.json` sudah di-commit ke GitHub
3. Tunggu maksimal 60 menit atau reload extension manual
4. Cek console browser untuk error (F12 → Console)

### Error "Failed to fetch"

**Penyebab:** GitHub down atau internet bermasalah

**Solusi:** Extension akan tetap berjalan normal (fail-safe mode)

### Private repository tidak bisa diakses

**Penyebab:** Raw URL untuk private repo memerlukan authentication

**Solusi:** 
1. Gunakan public repository, atau
2. Gunakan GitHub token untuk authentication (advanced)

## Security Notes

- ⚠️ **Jangan share URL raw Anda** jika tidak ingin orang lain tahu status extension
- ✅ **Gunakan private repository** untuk keamanan lebih
- ✅ **Update `lastUpdate` timestamp** setiap kali mengubah status untuk tracking

## Advanced Configuration

### Mengubah Interval Check

Edit `background.js`:
```javascript
const KILL_SWITCH_CHECK_INTERVAL = 30; // Ubah dari 60 menit ke 30 menit
```

### Mengubah Timeout

Edit `background.js`:
```javascript
const KILL_SWITCH_TIMEOUT = 5000; // Ubah dari 10 detik ke 5 detik
```

## Support

Jika mengalami masalah, silakan hubungi developer extension.
