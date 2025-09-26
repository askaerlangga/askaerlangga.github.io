---
layout: post
title:  "Mengakses Private Server menggunakan Tailscale"
date:   2025-09-26
tags: [tailscale,network]
---

<img src="/assets/images/tailscale.jpg" alt="thumbnail" />

Tailscale adalah sebuah VPN (Virtual Private Network) berbasis protokol WireGuard yang memungkinkan perangkat saling terhubung secara aman layaknya berada di jaringan lokal (LAN). Dengan Tailscale, pengguna dapat membangun jaringan pribadi yang terenkripsi, mudah dikonfigurasi, dan dapat diakses dari berbagai platform tanpa perlu pengaturan rumit seperti VPN tradisional.

Baca selengkapnya disini: [How Tailscale works][how-tailscale-works]

<img src="/assets/images/tailscale-personal-price.png" alt="tailscale-personal-price" />

Untuk penggunaan personal, kita diberi akses 3 user dan 100 device yang bisa terhubung.

### Create account:

Pertama, buat akun tailscale dulu disini: [https://login.tailscale.com][login-tailscale]

### Install Tailscale di Server:

Untuk server ubuntu dan server linux lainnya, masukan perintah:

{% highlight bash %}
curl -fsSL https://tailscale.com/install.sh | sh
{% endhighlight %}

Setelah itu akan muncul link untuk menghubungkan ke akun Tailscale, copy-paste link tersebut di browser lalu ikuti langkahnya.

### Install Tailscale di Client:

Jika client menggunakan Linux, lakukan hal yang sama seperti diatas.

Untuk sistem operasi lainnya seperti Windows, MacOS dan Android, bisa melihat caranya disini: [https://tailscale.com/download][download-tailscale]

<img src="/assets/images/tailscale-machine-list.png" alt="tailscale-machine-list" />

Jika berhasil maka akan muncul list perangkat yang terhubung ke akun kita.

Test ping ke server menggunakan IP Address yang ada disana:

{% highlight bash %}
ping IP_SERVER -c 4
{% endhighlight %}

<img src="/assets/images/tailscale-test-ping.png" alt="tailscale-test-ping" />

[how-tailscale-works]: https://tailscale.com/blog/how-tailscale-works
[login-tailscale]: https://login.tailscale.com
[download-tailscale]: https://tailscale.com/download