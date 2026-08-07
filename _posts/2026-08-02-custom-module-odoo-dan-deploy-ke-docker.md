---
layout: post
title:  "Membuat Custom Module Odoo dan Deploy ke Docker"
date:   2026-08-02
tags: [odoo, docker]
---

Ketika kebutuhan bisnis sudah tidak bisa lagi dipenuhi oleh module standar Odoo, langkah yang biasa diambil adalah membuat custom module sendiri — entah untuk menambah field baru, proses approval khusus, atau laporan yang sesuai kebutuhan perusahaan. Setelah Odoo berjalan di Docker (lihat artikel sebelumnya tentang [setup Odoo dengan Docker]({% post_url 2026-08-02-cara-setup-odoo-dengan-docker %})), langkah selanjutnya adalah membuat custom module sendiri dan menjalankannya di container tersebut. Berikut langkah-langkahnya.

### Struktur Folder Module

Di dalam folder `./addons` yang sudah kita mount ke `/mnt/extra-addons`, buat folder module baru, misalnya `my_custom_module`:

```bash
mkdir -p addons/my_custom_module/models
mkdir -p addons/my_custom_module/views
mkdir -p addons/my_custom_module/security
```

Struktur minimal sebuah module Odoo:

```
my_custom_module/
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   └── my_model.py
├── views/
│   └── my_model_views.xml
└── security/
    └── ir.model.access.csv
```

### File __manifest__.py

File ini berisi metadata module — nama, versi, dependency, dan daftar file yang harus di-load.

```python
{
    'name': 'My Custom Module',
    'version': '1.0',
    'summary': 'Contoh custom module Odoo',
    'category': 'Custom',
    'author': 'Aska Erlangga',
    'depends': ['base'],
    'data': [
        'security/ir.model.access.csv',
        'views/my_model_views.xml',
    ],
    'installable': True,
    'application': True,
}
```

### File __init__.py

Di root module, isi dengan:

```python
from . import models
```

Di `models/__init__.py`:

```python
from . import my_model
```

### Membuat Model

Isi `models/my_model.py` dengan model sederhana:

```python
from odoo import models, fields

class MyModel(models.Model):
    _name = 'my.custom.model'
    _description = 'My Custom Model'

    name = fields.Char(string='Name', required=True)
    description = fields.Text(string='Description')
    active = fields.Boolean(string='Active', default=True)
```

### Menambahkan Akses (Security)

Setiap model baru wajib punya access rights, isi `security/ir.model.access.csv`:

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_my_custom_model,my.custom.model,model_my_custom_model,,1,1,1,1
```

### Membuat View

Isi `views/my_model_views.xml` dengan form, list, dan menu:

```xml
<odoo>
    <record id="view_my_custom_model_form" model="ir.ui.view">
        <field name="name">my.custom.model.form</field>
        <field name="model">my.custom.model</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <group>
                        <field name="name"/>
                        <field name="description"/>
                        <field name="active"/>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

    <record id="view_my_custom_model_list" model="ir.ui.view">
        <field name="name">my.custom.model.list</field>
        <field name="model">my.custom.model</field>
        <field name="arch" type="xml">
            <list>
                <field name="name"/>
                <field name="active"/>
            </list>
        </field>
    </record>

    <record id="action_my_custom_model" model="ir.actions.act_window">
        <field name="name">My Custom Model</field>
        <field name="res_model">my.custom.model</field>
        <field name="view_mode">list,form</field>
    </record>

    <menuitem id="menu_my_custom_module_root" name="My Custom Module"/>
    <menuitem id="menu_my_custom_model" name="My Records"
              parent="menu_my_custom_module_root"
              action="action_my_custom_model"/>
</odoo>
```

### Deploy Module ke Container

Karena folder `./addons` sudah di-mount langsung ke container lewat `docker-compose.yml`, Odoo bisa langsung membaca module baru begitu container di-restart:

```bash
docker compose restart odoo
```

### Install Module Lewat UI

1. Buka `http://localhost:8069`, login sebagai admin.
2. Aktifkan **Developer Mode**: Settings > General Settings > Developer Tools > Activate the developer mode.
3. Buka menu **Apps**, klik **Update Apps List** lalu **Update**.
4. Hapus filter default, cari nama module `My Custom Module`.
5. Klik **Install**.

Setelah terinstall, menu **My Custom Module** akan muncul di sidebar utama, siap digunakan.

### Update Module Setelah Ubah Code

Setiap kali mengubah kode Python atau XML pada module, container perlu di-restart agar perubahan terbaca, lalu module di-upgrade agar perubahan (field baru, view baru) diterapkan ke database:

```bash
docker compose restart odoo
docker compose exec odoo odoo -d <nama_database> -u my_custom_module --stop-after-init
docker compose restart odoo
```

### Penutup

Dengan volume mount `./addons` di Docker Compose, workflow develop custom module Odoo jadi cepat — cukup edit file di host, restart/upgrade container, dan module langsung ter-refresh tanpa perlu rebuild image. Cocok untuk iterasi development sebelum module dinaikkan ke environment staging atau production.
