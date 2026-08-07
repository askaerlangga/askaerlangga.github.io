---
layout: post
title: "Belajar Odoo: Menambahkan item baru pada field Selection"
date: 2025-05-18
modified_date: 2025-09-28
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Dalam workflow bisnis, status sebuah dokumen sering bertambah seiring proses yang makin kompleks — misalnya awalnya cukup "draft" dan "confirm", lalu perusahaan butuh tahapan tambahan seperti "done" atau "cancel" untuk mencerminkan proses aktual di lapangan. Kalau field status ini ada di module orang lain atau module inti Odoo, kita tidak perlu mengubah kode aslinya — cukup manfaatkan mekanisme inherit untuk menambah pilihan baru.

### Parent Model:

Pada field **"state"** di model utama terdapat 2 item, yaitu **"draft"** dan **"confirm"**:

{% highlight python %}
class NamaModel(model.Models):
    _name = 'nama.model'
    _description = 'Deskripsi Model'

    state = fields.Selection([
        ('draft', 'Draft'),
        ('confirm', 'Confirm'),
    ], string='Status')
{% endhighlight %}

Kita akan coba menambahkan item baru, yaitu **"done"** dan **"cancel"**.

### Inherited Model:

Pada field state kita tambahkan attribute **"selection_add"**, tipe data attribute ini adalah **"list"**:

{% highlight python %}
class NamaModel(model.Models):
    _inherit = 'nama.model'

    state = fields.Selection(selection_add=[
        ('done', 'Done'),
        ('cancel', 'Canceled'),
    ])
{% endhighlight %}

Dengan `selection_add`, item baru langsung menyatu dengan pilihan yang sudah ada tanpa menghapus atau menimpa opsi lama — aman dipakai walau field tersebut sudah dipakai di banyak tempat (view, workflow, laporan) sebelum perubahan ini dibuat.