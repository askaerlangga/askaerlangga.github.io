---
layout: post
title: "Belajar Odoo: Menambahkan domain pada Report action"
date: 2025-11-29
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Domain pada action report berfungsi untuk membatasi record mana yang bisa menampilkan tombol print, contohnya ketika kita ingin membatasi record yang bisa print "Delivery Slip" hanya record yang berstatus "done".

<img src="/assets/images/button_printout_deliveryslip_delivery_order_status_done.png" />

<p style="text-align:center">Delivery Order Status: "done"</p>

<img src="/assets/images/button_printout_deliveryslip_delivery_order_status_waiting.png" />

<p style="text-align:center">Delivery Order Status: "waiting"</p>

Fitur domain ini sangat berguna karena pada odoo biasanya cara meng-handle kondisi tertentu adalah dengan membuat reportnya tetap bisa di-print tetapi hanya menampilkan kertas kosong, dan ini dilakukan pada template report nya bukan pada action report.

> Field domain ini tidak sengaja saya temukan ketika sedang custom report Delivery Slip di Odoo 18, dan anehnya pada saat tulisan ini dibuat (Odoo 19 sudah rilis) di dokumentasi Odoo belum ada [^1].

Contoh menambahkan domain pada action report Delivery Slip:

{% highlight xml %}
<record id="stock.action_report_delivery" model="ir.actions.report">
    <field name="domain">[('state', '=', 'done')]</field>
</record>
{% endhighlight %}

[^1]: [Dokumentasi Report Action Odoo 18 (Waktu tulisan ini dibuat)](https://web.archive.org/web/20251128175725/https://www.odoo.com/documentation/18.0/developer/reference/backend/actions.html#report-actions-ir-actions-report)
