---
layout: post
title:  "Cara Install Cloudflare WARP di Ubuntu"
date:   2020-10-06 20:00:00 +0700
categories: Cloudflare Ubuntu
---

<img src="/assets/images/cloudflare.jpg" />

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