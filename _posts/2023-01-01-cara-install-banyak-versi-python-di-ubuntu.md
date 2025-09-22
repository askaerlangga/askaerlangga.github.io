---
layout: post
title:  "Cara Install Banyak versi Python di Ubuntu"
date:   2023-01-01 20:00:00 +0700
tags: [ubuntu,python]
---

<img src="/assets/images/python.png" />

Ubuntu merupakan distro linux point release, yang dimana dia akan menggunakan versi aplikasi dan package yang ada pada saat dia rilis. Misalnya ketika Ubuntu 20.04 LTS dirilis, versi python yang ada saat itu adalah versi 3.8 maka dia menggunakan versi 3.8, sedangkan pada versi Ubuntu 22.04 LTS versi python yang ada pada saat itu adalah versi 3.10 maka dia menggunakan versi 3.10. Versi python ini tidak akan berganti atau tidak akan diupgrade ke versi diatasnya, hanya akan diupdate patch dan keamanan saja.

<figure>
    <img src="/assets/images/python3-package-version-ubuntu.png" />
    <figcaption>Versi python pada masing-masing versi ubuntu (packages.ubuntu.com)</figcaption>
</figure>

Sebagai developer, kita bisa saja ada keperluan untuk menggunakan versi tertentu dari python. Misalnya ketika library dari project yang kita buat tidak support versi python yang kita gunakan saat ini, maka mau tidak mau kita harus menggunakan versi lain.

### Install dengan menambahkan Repository PPA:

Tambahkan repository PPA milik **deadnakes** menggunakan terminal :

{% highlight bash %}
sudo add-apt-repository ppa:deadsnakes/ppa -y && sudo apt update
{% endhighlight %}

Setelah itu, kita bisa menginstall versi python yang diinginkan. Misalnya versi 3.8 :

{% highlight bash %}
sudo apt install python3.8
{% endhighlight %}

Semoga bermanfaat.