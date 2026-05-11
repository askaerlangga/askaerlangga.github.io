---
layout: post
title: "Belajar Odoo: Menambahkan menu Print dinamis menggunakan get_extra_print_items"
date: 2026-05-11
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Pada Odoo 18, model **account.move** (Invoice, Bill, dll) memiliki method baru yaitu `get_extra_print_items`. Method ini berfungsi untuk menambahkan item pada menu "Print" secara dinamis melalui Python, sehingga kita tidak perlu lagi mendefinisikan `ir.actions.report` yang statis di XML jika kondisinya berubah-ubah.

Fitur ini sangat berguna ketika kita ingin menampilkan tombol download untuk dokumen yang sudah ada (seperti attachment EDI atau dokumen legal yang sudah ditandatangani) tanpa harus melakukan *re-generate* PDF.

Contoh kasus: Kita ingin menambahkan menu **"Download Signed Document"** hanya ketika Invoice sudah berstatus **"posted"**.

### Implementasi pada account.move:

{% highlight python %}
class AccountMove(models.Model):
    _inherit = 'account.move'

    def get_extra_print_items(self):
        # Ambil item default
        res = super().get_extra_print_items()

        for move in self:
            # Hanya muncul jika sudah posted dan merupakan invoice
            if move.state == 'posted' and move.is_invoice():
                res.append({
                    'key': 'download_signed_doc',
                    'description': _('Download Signed Document'),
                    'type': 'ir.actions.act_url',
                    'url': f'/web/content/xxx', # URL ke attachment
                    'target': 'download',
                })

        return res
{% endhighlight %}

Method ini nantinya akan dipanggil oleh komponen OWL di frontend untuk mengisi daftar pilihan pada tombol Print. Dengan cara ini, kita memiliki kontrol penuh dari sisi backend untuk menentukan kapan sebuah menu cetak harus muncul berdasarkan logika bisnis tertentu.

### Alternatif Lain: Menggunakan Domain pada Action Report

Jika kebutuhan Anda hanya sekadar membatasi kemunculan menu print standar berdasarkan kondisi field tertentu (misal: hanya muncul jika status 'done'), Anda juga bisa menggunakan cara yang lebih sederhana melalui XML dengan menambahkan field **domain** pada action report terkait.

Caranya bisa dilihat pada postingan sebelumnya: [Belajar Odoo: Menambahkan domain pada Report action]({% post_url 2025-11-29-belajar-odoo-menambahkan-domain-pada-report-action %}).

