---
layout: post
title:  "Belajar Git: Install dan Konfigurasi Awal"
date:   2023-08-10 20:00:00 +0700
tags: [git]
---

<img src="/assets/images/git.png" />

Pada materi ini kita akan belajar cara menginstall dan konfigurasi awal git.

### Install git di Ubuntu :

Buka terminal lalu ketikan perintah:

{% highlight bash %}
sudo apt install git
{% endhighlight %}

Setelah itu cek apakah sudah terinstall atau belum dengan perintah:

{% highlight bash %}
git --version
{% endhighlight %}

Jika sudah berhasil terinstall maka tampilannya akan seperti ini:

<img src="/assets/images/git-version.png" />

### Konfigurasi Awal :

Setelah berhasil menginstall git, kita harus menetapkan username dan email untuk git. Hal ini berguna ketika nanti kita melakukan perintah commit.
Masukan perintah berikut di terminal dan sesuaikan dengan username dan email kamu:

{% highlight bash %}
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
{% endhighlight %}

Setelah selesai, cek dengan perintah berikut:

{% highlight bash %}
git config --list
{% endhighlight %}