---
layout: post
title:  "Hosting Web Statis di GitHub Pages"
date:   2025-11-30
tags: [github, github-pages]
---

<p style="text-align:center"><img src="/assets/images/github-pages.png" alt="GitHub Pages" /></p>

GitHub Pages adalah layanan hosting gratis dari GitHub yang memungkinkan kita untuk membuat dan menampilkan situs web statis langsung dari repository. Tanpa perlu server khusus atau konfigurasi rumit, kamu bisa mempublikasikan website hanya dengan beberapa klik atau push code.

### Login ke GitHub

Untuk membuatnya, kamu perlu login ke akun GitHubmu. Jika tidak punya bisa membuat akun di [GitHub](https://github.com).

<p style="text-align:center"><img src="/assets/images/github-login-page.png" alt="GitHub Login" /></p>

### Membuat Repository

Buat repository baru dengan mengklik tombol Plus (+) di pojok kanan atas, lalu pilih "New repository".

<p style="text-align:center"><img src="/assets/images/create-new-repository.png" alt="Create Repository" /></p>

Isi nama repository dengan format `username.github.io`, misalnya `askaerlangga.github.io`. Pastikan repository diatur sebagai Public.

<p style="text-align:center"><img src="/assets/images/create-new-repository-detail.png" alt="Create Repository details" /></p>

Setelah itu, klik tombol "Create repository".

Maka hasilnya akan seperti gambar berikut:

<p style="text-align:center"><img src="/assets/images/create-new-repository-success.png" alt="Create Repository details" /></p>

### Menambahkan File .html

Setelah repository dibuat, tambahkan file `index.html` dengan mengklik "Creating a new file".

<p style="text-align:center"><img src="/assets/images/create-new-file-html.png" alt="Create new files" /></p>

Beri nama file `index.html`, lalu sementara isi dengan "Hello World" seperti berikut, lalu klik "Commit changes".

<p style="text-align:center"><img src="/assets/images/create-new-file-html-detail.png" alt="Create index.html" /></p>

Isi kolom commit message sesuai keinginanmu misalnya "add: index.html", lalu klik "Commit changes".

<p style="text-align:center"><img src="/assets/images/create-new-file-html-commit.png" alt="Commit Message" /></p>

### Mengaktifkan GitHub Pages

Setelah file `index.html` ditambahkan, sekarang aktifkan GitHub Pages dengan masuk ke tab "Settings" di repository.

Lalu pilih menu "Pages" di sidebar kiri. Pada bagian "Branch", ganti `None` menjadi `main`, lalu klik "Save". 

<p style="text-align:center"><img src="/assets/images/github-pages-settings.png" alt="Github Pages Settings" /></p>

Setelah itu tunggu beberapa saat dan kamu bisa mengaksesnya melalui URL `https://username.github.io`, misalnya `https://askaerlangga.github.io`.