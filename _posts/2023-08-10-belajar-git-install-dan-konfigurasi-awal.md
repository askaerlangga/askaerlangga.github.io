---
layout: post
title:  "Belajar Git: Install dan Konfigurasi Awal"
date:   2023-08-10 20:00:00 +0700
tags: [git]
---

<img src="/assets/images/git.png" />

Ketika kerja bareng tim atau butuh mencatat setiap perubahan kode secara rapi, git jadi tools paling dasar yang wajib disiapkan sebelum mulai kolaborasi — baik untuk push ke GitHub, kerja bareng developer lain, atau sekadar menyimpan history perubahan sendiri. Sebelum bisa dipakai, ada dua langkah wajib: install git, lalu konfigurasi identitas kita di dalamnya. Berikut caranya di Ubuntu.

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

Username dan email ini yang nantinya akan tercatat di setiap commit yang kita buat, jadi pastikan sudah benar sebelum mulai kerja — supaya history project kita rapi dan jelas siapa yang mengubah apa.