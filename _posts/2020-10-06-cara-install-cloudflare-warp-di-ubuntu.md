---
layout: post
title:  "Cara Install Cloudflare WARP di Ubuntu"
date:   2020-10-06 20:00:00 +0700
tags: [cloudflare,ubuntu]
---

<img src="/assets/images/cloudflare.jpg" />

Cloudflare WARP adalah client VPN gratis dari Cloudflare yang mengenkripsi traffic internet kita dan melewatkannya lewat network Cloudflare (1.1.1.1). Selain untuk privasi, WARP juga sering dipakai untuk mengatasi koneksi yang lambat atau diblokir ISP tertentu, tanpa perlu setting VPN yang rumit. Berikut cara install-nya di Ubuntu.

### Tambahkan Repository

Buka terminal lalu tambahkan repository GPG key :

{% highlight bash %}
curl https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg
{% endhighlight %}

Setelah itu tambahkan alamat repository :

{% highlight bash %}
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list
{% endhighlight %}

Update repository dan Install package-nya:

{% highlight bash %}
sudo apt update && sudo apt install cloudflare-warp
{% endhighlight %}

Jika sudah diinstall, kita harus daftarkan terlebih dahulu warp client nya :

{% highlight bash %}
warp-cli register
{% endhighlight %}

Lalu baru kita koneksikan :

{% highlight bash %}
warp-cli connect
{% endhighlight %}

Setelah itu cek apakah sudah berjalan dengan perintah :

{% highlight bash %}
curl https://www.cloudflare.com/cdn-cgi/trace/
{% endhighlight %}

Pastikan terdapat tulisan **warp=on**

Kalau sudah muncul `warp=on`, artinya semua traffic dari perangkat kita sudah lewat jaringan Cloudflare. Simpel, gratis, dan bisa langsung dimatikan lagi kapan saja dengan `warp-cli disconnect` kalau sedang tidak dibutuhkan.