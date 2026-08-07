---
layout: post
title: "Belajar Odoo: Field multiple file upload"
date: 2025-05-18
modified_date: 2025-09-26
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Dari sisi proses kerja HR misalnya, satu karyawan biasanya perlu melampirkan lebih dari satu dokumen dalam satu proses input data — KTP, KK, surat keterangan, dan sebagainya. Kalau di form hanya disediakan satu kolom upload, user jadi bingung dokumen mana harus ditaruh di mana, atau malah harus minta tambah field baru setiap kali ada jenis dokumen lain. Kebutuhan seperti ini sebenarnya bisa dipenuhi dengan satu field saja, secara teknis menggunakan relasi `many2many` ke model `ir.attachment`.

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

Hasilnya, dari sisi user cukup satu tempat upload untuk semua dokumen pendukung, lengkap dengan preview dan tombol hapus per file. Tidak perlu lagi menambah field baru setiap kali ada jenis dokumen tambahan, dan proses input data jadi lebih sederhana bagi tim yang menggunakannya.