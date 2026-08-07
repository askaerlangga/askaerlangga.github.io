---
layout: post
title:  'Mengatasi Warning: database "postgres" has a collation version mismatch pada Postgresql'
date:   2024-07-06
modified_date: 2025-10-30
tags: [postgresql,database]
---

<img src="/assets/images/postgresql.png" alt="thumbnail" />

Warning ini sering muncul setelah server di-upgrade OS-nya (misalnya dari Ubuntu 22.04 ke 24.04), karena versi library collation di sistem berubah sementara database yang sudah ada masih mencatat versi collation yang lama. Meski biasanya tidak langsung mengganggu operasional, warning ini sebaiknya tidak dibiarkan karena berpotensi menyebabkan hasil sorting/pencarian data yang tidak konsisten. Berikut cara mengatasinya.

Masuk ke PSQL:

{% highlight bash %}
sudo -u postgres psql
{% endhighlight %}

Lalu masukan query:

{% highlight bash %}
REINDEX DATABASE nama_database;
ALTER DATABASE nama_database REFRESH COLLATION VERSION;
{% endhighlight %}

Contoh database yang terkena warning collation version adalah "postgres":

{% highlight bash %}
REINDEX DATABASE postgres;
ALTER DATABASE postgres REFRESH COLLATION VERSION;
{% endhighlight %}

Setelah query ini dijalankan, PostgreSQL akan mencatat ulang versi collation sesuai library yang aktif di sistem, dan warning collation version mismatch tidak akan muncul lagi untuk database tersebut.