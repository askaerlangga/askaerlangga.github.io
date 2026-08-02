---
layout: post
title:  "Cara Setup Odoo dengan Docker"
date:   2026-08-02
tags: [odoo, docker]
---

Docker memudahkan kita menjalankan Odoo tanpa perlu install Python, PostgreSQL, dan dependency lainnya secara manual di sistem. Cukup dengan `docker compose`, Odoo bisa langsung jalan dalam hitungan menit. Berikut langkah-langkahnya.

### Prasyarat

Pastikan Docker dan Docker Compose sudah terinstall di komputer/server kamu. Cek dengan:

```bash
docker --version
docker compose version
```

Kalau belum ada, install dulu Docker mengikuti panduan resmi di [docs.docker.com](https://docs.docker.com/engine/install/).

### Membuat docker-compose.yml

Buat folder project, misalnya `odoo-docker`, lalu buat file `docker-compose.yml` di dalamnya:

```bash
mkdir odoo-docker && cd odoo-docker
```

Isi `docker-compose.yml` dengan konfigurasi berikut:

```yaml
version: '3.8'
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: odoo
      POSTGRES_PASSWORD: odoo
      POSTGRES_DB: postgres
    volumes:
      - odoo-db-data:/var/lib/postgresql/data

  odoo:
    image: odoo:19
    depends_on:
      - db
    ports:
      - "8069:8069"
    environment:
      - HOST=db
      - USER=odoo
      - PASSWORD=odoo
    volumes:
      - odoo-web-data:/var/lib/odoo
      - ./addons:/mnt/extra-addons

volumes:
  odoo-db-data:
  odoo-web-data:
```

Penjelasan singkat:
- Service `db` menjalankan PostgreSQL sebagai database Odoo.
- Service `odoo` menjalankan image resmi Odoo, terhubung ke database lewat variabel `HOST`.
- Folder `./addons` di-mount ke `/mnt/extra-addons`, tempat kita nanti menaruh custom module.
- Volume `odoo-db-data` dan `odoo-web-data` menyimpan data database dan filestore agar tidak hilang saat container di-restart.

### Menjalankan Container

Jalankan dengan perintah:

```bash
docker compose up -d
```

Cek status container:

```bash
docker compose ps
```

Kalau statusnya `Up`, berarti Odoo sudah berjalan.

### Membuka Odoo di Browser

Buka browser dan akses `http://localhost:8069`. Odoo akan menampilkan halaman untuk membuat database baru — isi nama database, email, dan password admin, lalu klik "Create database".

Setelah database dibuat, kamu akan langsung masuk ke dashboard Odoo dan siap digunakan.

### Menambahkan Custom Module

Untuk mengembangkan module sendiri, cukup taruh folder module-nya di dalam `./addons` yang sudah kita mount tadi. Setelah itu restart container agar Odoo membaca ulang addons path:

```bash
docker compose restart odoo
```

Lalu install module lewat menu **Apps** di Odoo (aktifkan dulu mode developer di Settings > General Settings > Developer Tools > Activate the developer mode, lalu klik "Update Apps List").

### Menghentikan dan Menghapus Container

Untuk menghentikan Odoo tanpa menghapus data:

```bash
docker compose stop
```

Untuk menghapus container beserta volume (data akan hilang, gunakan hati-hati):

```bash
docker compose down -v
```

### Penutup

Dengan Docker Compose, setup Odoo jadi jauh lebih simpel dan reproducible — cukup satu file konfigurasi, semua environment (database, addons, port) sudah terdefinisi rapi. Cocok untuk eksperimen lokal maupun basis untuk deployment produksi (dengan tambahan reverse proxy dan tuning tentunya).
