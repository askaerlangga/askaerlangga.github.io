---
layout: post
title:  "Kenapa Perubahan ir.rule di Odoo Kadang Tidak Langsung Terasa (ormcache)"
date:   2026-08-06
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Pernah mengalami kasus begini: sebuah `ir.rule` (record rule) di Odoo bergantung pada nilai field custom di `res.users`, misalnya daftar tag/tipe yang boleh diakses user. Ketika field itu diubah — katakanlah ditambah satu opsi lagi — user yang bersangkutan tetap saja hanya melihat data lama, seolah perubahan tidak pernah disimpan. Padahal kalau dicek langsung ke database, nilainya sudah benar.

Ini bukan bug di logic domain-nya, dan bukan salah setting. Ini soal **caching di level ORM** yang sering tidak disadari ketika kita membuat `ir.rule` dinamis berbasis field pada `res.users`.

### Akar masalahnya: `_compute_domain` di-cache dengan ormcache

Odoo meng-cache hasil evaluasi domain dari `ir.rule` supaya tidak perlu di-eval ulang (safe_eval) setiap kali ada query. Di `odoo/addons/base/models/ir_rule.py`:

{% highlight python %}
@api.model
@tools.conditional(
    'xml' not in config['dev_mode'],
    tools.ormcache('self.env.uid', 'self.env.su', 'model_name', 'mode',
                   'tuple(self._compute_domain_context_values())'),
)
def _compute_domain(self, model_name, mode="read"):
    ...
{% endhighlight %}

Perhatikan cache key-nya: `(uid, su, model_name, mode, allowed_company_ids)`. Kalau domain rule Anda memakai field lain di `res.users` — bukan `allowed_company_ids` — field itu **tidak ikut jadi bagian cache key**. Artinya, begitu domain untuk user tersebut pernah di-hitung sekali dan disimpan di cache, Odoo tidak tahu kapan harus menghitung ulang hanya karena field custom itu berubah.

Contoh domain rule yang rawan kena masalah ini:

{% highlight python %}
<field name="domain_force">
  [('product_type_id', 'in', user.allowed_product_type_ids.ids)]
  if user.allowed_product_type_ids else [(1, '=', 1)]
</field>
{% endhighlight %}

Field `allowed_product_type_ids` di sini adalah Many2many custom pada `res.users`. Selama field itu belum pernah diubah setelah cache pertama terbentuk, semua terlihat normal. Begitu diedit — user tetap terkunci ke hasil domain versi lama, sampai ada sesuatu yang membersihkan ormcache (misalnya restart worker Odoo, atau upgrade module).

### Cara memastikan ini memang gejalanya

Sebelum menyalahkan cache, verifikasi dulu bahwa data memang sudah benar tapi behavior belum berubah:

1. Baca ulang nilai field langsung dari database/API — pastikan memang sudah tersimpan sesuai yang diinginkan.
2. Cek apakah domain `ir.rule` benar secara logika (baca `domain_force`-nya, coba eval manual).
3. Kalau keduanya sudah benar tapi user masih melihat hasil lama — kemungkinan besar ini soal cache, bukan soal domain atau data.

### Solusinya: invalidasi cache manual saat field berubah

Cara paling aman adalah override `write()` pada `res.users`, dan panggil `clear_caches()` setiap kali field pemicu domain itu berubah:

{% highlight python %}
class ResUsers(models.Model):
    _inherit = 'res.users'

    allowed_product_type_ids = fields.Many2many(
        comodel_name='product.type',
        relation='res_users_product_type_rel',
        column1='user_id',
        column2='product_type_id',
        string='Allowed Detail Product Types',
    )

    def write(self, vals):
        res = super().write(vals)
        if 'allowed_product_type_ids' in vals:
            # ir.rule._compute_domain() di-cache (ormcache) dengan key yang
            # TIDAK mencakup field ini. Tanpa clear_caches(), user akan tetap
            # melihat hasil domain versi lama sampai cache di-invalidate oleh
            # hal lain (restart worker, module upgrade, dst).
            self.env['ir.rule'].clear_caches()
        return res
{% endhighlight %}

`clear_caches()` di sini memanggil `ormcache.clear_caches()` milik model `ir.rule`, jadi semua entry cache `_compute_domain` untuk seluruh user ikut dibersihkan (bukan hanya user yang bersangkutan — memang begitu cara kerja ormcache, granularity-nya per-model bukan per-record). Untuk kasus perubahan setting akses yang jarang terjadi (bukan operasi bervolume tinggi), trade-off ini wajar.

Kalau sekadar butuh solusi cepat tanpa ubah kode — restart worker Odoo atau lakukan module upgrade juga akan membersihkan ormcache, tapi itu bukan solusi permanen karena masalahnya akan muncul lagi setiap kali field itu diubah lagi.

### Kapan perlu waspada soal ini

Pola ini relevan setiap kali Anda membuat `ir.rule` yang domain-nya bergantung pada field custom di `res.users` (atau model lain yang jadi context eval domain), bukan field bawaan seperti `allowed_company_ids` yang sudah otomatis termasuk dalam cache key. Aturan praktisnya: **kalau Anda menambahkan field baru sebagai variabel dalam `domain_force` sebuah `ir.rule`, selalu pasang invalidasi cache manual di `write()` model tempat field itu berada.** Kalau tidak, perubahan setting akan terlihat "tidak berpengaruh" — padahal sebenarnya cuma menunggu cache lama habis.
