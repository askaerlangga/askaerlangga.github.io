---
layout: post
title:  "Cara Mengaktifkan Layanan Ubuntu Pro Gratis"
date:   2020-10-08 20:00:00 +0700
categories: Ubuntu-Pro Ubuntu
---

<img src="/assets/images/ubuntu-pro.jpg" />

Halo teman-teman, Ubuntu Pro merupakan rebranding dari Ubuntu Advantage, yaitu sebuah layanan yang diberikan oleh Canonical perusahaan induk dari Ubuntu yang bisa memberikan update security untuk Ubuntu LTS sampai dengan 10 tahun (dukungan Ubuntu LTS 5 Tahun + Ubuntu Pro 5 Tahun). Jadi ketika teman-teman mengaktifkan Ubuntu Pro, kita akan mendapatkan tambahan 5 Tahun update security.

Layanan ini tentunya berbayar, tetapi teman-teman tidak perlu khawatir karena [Canonical memberikan akses gratis][free-personal-ubuntu-blog] Ubuntu Pro untuk personal sampai dengan 5 perangkat

<img src="/assets/images/ubuntu-pro-website.png" />

Untuk cara mengaktifkanya, teman-teman harus membuat akun Ubuntu One terlebih dahulu di link berikut :

[https://ubuntu.com/pro][ubuntu-pro]

<img src="/assets/images/ubuntu-pro-free-personal-token.png" />

Setelah itu kamu akan menemukan Token dan cara mengaktifkan layanan Ubuntu Pro.

Untuk mengaktifkannya, pastikan package ubuntu-advantage-tools sudah terinstall di komputer kita, cara installnya cukup buka terminal lalu ketikan :

{% highlight bash %}
sudo apt install ubuntu-advantage-tools
{% endhighlight %}

Jika sudah terinstall, kita bisa langsung mengaktifkannya dengan mengetikan :

{% highlight bash %}
sudo pro attach (TOKEN)
{% endhighlight %}

atau

{% highlight bash %}
sudo ua attach (TOKEN)
{% endhighlight %}

Tunggu prosesnya sampai selesai, lalu cek apakah sudah aktif dengan cara mengetikan :

{% highlight bash %}
sudo ua status
{% endhighlight %}

<img src="/assets/images/ubuntu-pro-status.png" />

Jika sudah aktif, maka akan terlihat seperti gambar di atas.

[free-personal-ubuntu-blog]: https://ubuntu.com/blog/ubuntu-pro-beta-release
[ubuntu-pro]: https://ubuntu.com/pro