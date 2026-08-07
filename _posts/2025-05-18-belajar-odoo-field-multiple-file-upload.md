---
layout: post
title: "Belajar Odoo: Field multiple file upload"
date: 2025-05-18
modified_date: 2025-09-26
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Pernah butuh upload lebih dari satu file ke satu record, misalnya lampiran KTP, KK, dan surat keterangan sekaligus di satu form karyawan? Kalau pakai field `Binary` biasa, kita cuma bisa upload satu file per field — mau tambah lampiran lain berarti tambah field baru lagi. Solusinya di Odoo cukup sederhana: manfaatkan relasi `many2many` ke model `ir.attachment`.

### Tambahkan Field di Model

Tambahkan field "many2many" dengan model "ir.attachment":

{% highlight python %}
attachment_ids = fields.Many2many(
	"ir.attachment",
	string='Upload Document'
)
{% endhighlight %}

### Tambahkan di View

lalu tambahkan juga di views dengan widget "many2many_binary":

{% highlight xml %}
<field name="attachment_ids" widget="many2many_binary" />
{% endhighlight %}

Setelah kedua langkah ini, user bisa upload banyak file sekaligus ke field yang sama, lengkap dengan tampilan preview dan tombol hapus per file — tanpa perlu bikin field terpisah untuk setiap dokumen.