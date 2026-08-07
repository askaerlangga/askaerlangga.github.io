---
layout: post
title:  "Belajar Odoo: Inherit dan Override Onchange Method"
date:   2025-04-26
modified_date: 2025-09-26
tags: [odoo]
---

<img src="/assets/images/odoo.png" alt="thumbnail" />

Dalam banyak proses bisnis, satu perubahan input sering perlu memicu perubahan pada input lain secara otomatis — supaya user tidak perlu mengisi manual dan risiko salah data berkurang. Contoh kasus yang umum: sebuah lahan parkir punya dua tipe kendaraan (motor dan mobil), dan setiap tipe punya lokasi parkir serta tarif yang berbeda. Ketika field "tipe kendaraan" berubah, selain mengubah "tempat parkir" maka akan mengubah "nominal parkir" juga — dan aturan ini bisa diperluas lewat inherit tanpa mengubah logic yang sudah ada di module utama.

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

Dengan pola inherit seperti ini, penambahan logic baru tidak perlu mengubah kode module aslinya sama sekali — cukup override method-nya, panggil `super()` supaya logic lama tetap berjalan, lalu tambahkan aturan bisnis baru di atasnya. Cara ini juga memudahkan saat module utama di-update, karena perubahan kita tetap terpisah dan aman dari konflik.