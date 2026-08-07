---
layout: post
title:  "Membuat Custom Module Odoo dan Deploy ke Docker"
date:   2026-08-02
tags: [odoo, docker]
---

Ketika kebutuhan bisnis sudah tidak bisa lagi dipenuhi oleh module standar Odoo, langkah yang biasa diambil adalah membuat custom module sendiri — entah untuk menambah field baru, proses approval khusus, atau laporan yang sesuai kebutuhan perusahaan. Setelah Odoo berjalan di Docker (lihat artikel sebelumnya tentang [setup Odoo dengan Docker]({% post_url 2026-08-02-cara-setup-odoo-dengan-docker %})), tantangan berikutnya biasanya bukan lagi soal menulis kode module-nya, tapi bagaimana men-deploy dan meng-update module tersebut di container secara konsisten. Artikel ini fokus ke bagian deploy-nya.

### Ringkasan Struktur Module

Sebagai gambaran singkat, module Odoo minimal terdiri dari beberapa file: `__manifest__.py` (metadata module), `__init__.py` (entry point Python), folder `models/` (logic dan field), `security/ir.model.access.csv` (hak akses), dan `views/` (tampilan form, list, menu). Semua file ini ditaruh di dalam folder `./addons` yang sudah kita mount ke `/mnt/extra-addons` lewat `docker-compose.yml`, misalnya di `addons/my_custom_module/`.

```
my_custom_module/
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   └── my_model.py
├── views/
│   └── my_model_views.xml
└── security/
    └── ir.model.access.csv
```

Detail cara menulis kode model, view, dan security tidak dibahas lagi di sini — fokus kita selanjutnya adalah bagaimana module ini benar-benar berjalan di container.

### Deploy Module ke Container

Karena folder `./addons` sudah di-mount langsung ke container lewat `docker-compose.yml`, Odoo bisa langsung membaca module baru begitu container di-restart — tidak perlu rebuild image sama sekali:

```bash
docker compose restart odoo
```

Restart ini membuat Odoo membaca ulang **addons path**, sehingga module baru yang ditaruh di folder tersebut baru dikenali oleh sistem (statusnya masih "Uninstalled" di menu Apps).

### Install Module Lewat UI

1. Buka `http://localhost:8069`, login sebagai admin.
2. Aktifkan **Developer Mode**: Settings > General Settings > Developer Tools > Activate the developer mode.
3. Buka menu **Apps**, klik **Update Apps List** lalu **Update**.
4. Hapus filter default, cari nama module `My Custom Module`.
5. Klik **Install**.

Setelah terinstall, menu **My Custom Module** akan muncul di sidebar utama, siap digunakan.

### Install/Update Lewat CLI (Tanpa UI)

Kalau sedang setup ulang environment atau butuh deploy tanpa membuka browser (misalnya lewat script CI/CD), instalasi bisa dilakukan langsung lewat command line di dalam container:

```bash
docker compose exec odoo odoo -d <nama_database> -i my_custom_module --stop-after-init
```

Flag `-i` (install) dipakai untuk module yang belum pernah diinstall, sementara `-u` (update) dipakai untuk module yang sudah terinstall tapi kodenya baru diubah. Flag `--stop-after-init` penting supaya proses Odoo langsung berhenti setelah instalasi selesai, bukan tetap berjalan sebagai server (yang bisa bikin dua proses Odoo rebutan port).

### Update Module Setelah Ubah Code

Setiap kali mengubah kode Python atau XML pada module, container perlu di-restart agar perubahan terbaca, lalu module di-upgrade agar perubahan (field baru, view baru) diterapkan ke database:

```bash
docker compose restart odoo
docker compose exec odoo odoo -d <nama_database> -u my_custom_module --stop-after-init
docker compose restart odoo
```

Urutannya penting: restart pertama supaya Odoo membaca kode Python terbaru, `-u` untuk migrasi schema/view ke database, dan restart kedua untuk memastikan proses server berjalan normal kembali (bukan proses `--stop-after-init` yang sudah berhenti).

### Verifikasi Deploy Berhasil

Setelah restart/upgrade, jangan langsung asumsikan berhasil — cek dulu log container untuk memastikan tidak ada error saat loading module:

```bash
docker logs -f odoo19 --tail 50
```

Kalau ada error saat load module (misalnya typo di XML atau field yang salah referensi), Odoo biasanya tetap `Up` di `docker compose ps` tapi module gagal terinstall/upgrade secara diam-diam. Jadi selalu cek log untuk memastikan, jangan hanya mengandalkan status container.

### Penutup

Dengan volume mount `./addons` di Docker Compose, proses deploy custom module Odoo jadi cepat — cukup edit file di host, restart/upgrade container lewat UI atau CLI, dan module langsung ter-refresh tanpa perlu rebuild image. Cocok untuk iterasi development sebelum module dinaikkan ke environment staging atau production, dengan catatan tetap verifikasi lewat log setiap kali deploy.
