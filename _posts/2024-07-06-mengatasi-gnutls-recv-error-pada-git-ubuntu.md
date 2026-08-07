---
layout: post
title:  'Mengatasi "GnuTLS recv error (-110): The TLS connection was non-properly terminated" pada Git Ubuntu'
date:   2024-07-06 20:00:00 +0700
tags: [git,ubuntu]
---

<img src="/assets/images/git.png" />

Error ini biasanya muncul tiba-tiba saat sedang `git clone` atau `git push`, dan cukup membingungkan karena sekilas terlihat seperti masalah koneksi internet — padahal akar masalahnya ada di konfigurasi git itu sendiri. Hal ini terjadi karena pada git yang terinstall di ubuntu menggunakan GnuTLS bukan OpenSSL.

Untuk mengatasinya kita perlu build ulang git nya dan menggunakan package libcurl4-openssl-dev dibandingkan libcurl4-gnutls-dev.

Untuk caranya bisa membuka link StackOverflow:

[https://stackoverflow.com/questions/52529639/gnutls-recv-error-110-the-tls-connection-was-non-properly-terminated][gnutls-recv-error]

Namun saya menemukan script yang otomatis:

[https://github.com/niko-dunixi/git-openssl-shellscript][git-openssl-shellscript]

Clone lalu jalankan compile-git-with-openssl.sh dan tunggu sampai selesai.

**Tested on: Ubuntu 24.04**

Setelah proses compile selesai, error GnuTLS ini tidak akan muncul lagi karena git sudah menggunakan OpenSSL sebagai library koneksi TLS-nya — clone dan push ke repository dengan koneksi HTTPS pun kembali berjalan normal.

[gnutls-recv-error]: https://stackoverflow.com/questions/52529639/gnutls-recv-error-110-the-tls-connection-was-non-properly-terminated
[git-openssl-shellscript]: https://github.com/niko-dunixi/git-openssl-shellscript