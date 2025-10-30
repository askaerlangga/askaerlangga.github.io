---
layout: post
title:  'Mengatasi Warning: database "postgres" has a collation version mismatch pada Postgresql'
date:   2024-07-06
update_date: 2025-10-30
tags: [postgresql,database]
---

<img src="/assets/images/postgresql.png" alt="thumbnail" />

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