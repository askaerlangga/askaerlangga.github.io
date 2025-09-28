---
layout: post
title: "Belajar Odoo: Mendefinisikan data awal"
date: 2025-05-29
update_date: 2025-09-28
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Data awal akan dibuat saat kita pertamakali install modul.

Buat file **".xml"** di dalam folder **"data"** (nama_modul/data), isinya:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<!-- noupdate="1" berfungsi agar
data tidak ditimpa saat update modul -->
<odoo noupdate="1">

    <record id="nama_id_record" model="nama.model">
    	<!-- Field-field yang ada di model -->
        <field name="field_1">Nama Record</field>
        <field name="field_2">2</field>
    </record>
    
</odoo>
{% endhighlight %}

Contoh saya membuat file **"tipe_kendaraan_data.xml"** di dalam folder **"data"**:

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<odoo noupdate="1">
	
    <record id="tipe_kendaraan_motor" model="tipe.kendaraan">
        <field name="name">Motor</field>
        <field name="jumlah_roda">2</field>
        <field name="biaya_parkir">3000</field>
    </record>
    
    <record id="tipe_kendaraan_mobil" model="tipe.kendaraan">
        <field name="name">Mobil</field>
        <field name="jumlah_roda">4</field>
        <field name="biaya_parkir">8000</field>
    </record>
    
</odoo>
{% endhighlight %}

Masukan file **"tipe_kendaraan_data.xml"** yang sudah dibuat di manifest:

{% highlight python %}
"version": "18.0.1.0.0",
    'depends': ['base'],
    'data': [
        'data/tipe_kendaraan_data.xml',
        'security/ir.model.access.csv',
        'views/tipe_kendaraan_views.xml',
	],
{% endhighlight %}