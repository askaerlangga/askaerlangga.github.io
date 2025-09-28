---
layout: post
title: "Belajar Odoo: Menambahkan item baru pada field Selection"
date: 2025-05-18
update_date: 2025-09-28
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

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