---
layout: post
title:  "Belajar Odoo: Inherit dan Override Onchange Method"
date:   2025-04-26
update_date: 2025-09-26
tags: [odoo]
---

<img src="/assets/images/odoo.png" alt="thumbnail" />

Contoh kasus: ketika field "tipe kendaraan" berubah, selain mengubah "tempat parkir" maka akan mengubah "nominal parkir" juga.

Model utama:

{% highlight python %}
class NamaModel(model.Models):
    _name = 'nama.model'
    _description = 'Deskripsi Model'

    type = fields.Selection([
        ('motor', 'Motor'),
        ('mobil', 'Mobil'),
    ], string='Tipe Kendaraan')

    tempat = fields.Selection([
        ('lapangan_a', 'Lapangan A'),
        ('lapangan_b', 'Lapangan B'),
    ], string='Tempat Parkir')

    # Onchange utama
    @api.onchange('type')
    def _onchange_type(self):
        for rec in self:
            rec.tempat = 'lapangan_a' if rec.type == 'motor' else 'lapangan_b'
{% endhighlight %}

Inherit model dan override method:

{% highlight python %}
class NamaModel(model.Models):
    _inherit = 'nama.model'

    # Tambahkan field parkir
    parkir = fields.Float('Nominal Parkir')

    # Override method
    @api.onchange('type')
    def _onchange_type(self):
        # Panggil method super() untuk memanggil method utama
        super()._onchange_type()

        # Masukan yang ingin ditambahkan
        for rec in self:
            rec.parkir = 5000 if rec.type == 'mobil' else 2000

{% endhighlight %}