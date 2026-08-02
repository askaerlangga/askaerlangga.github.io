---
layout: post
title:  "Debugging Odoo yang Berjalan di Docker"
date:   2026-08-02
tags: [odoo, docker, debugging]
---

Setelah Odoo jalan di Docker dan custom module ter-install (lihat dua artikel sebelumnya), cepat atau lambat kita akan menemui bug. Masalahnya, debugging aplikasi yang jalan di dalam container sedikit berbeda dari debugging biasa karena kode Python-nya "terkurung" di dalam container, terpisah dari environment host. Berikut beberapa teknik yang bisa dipakai.

### 1. Cek Log Container Dulu

Langkah paling murah dan sering langsung ketemu akar masalahnya:

```bash
docker logs -f odoo19 --tail 100
```

Traceback Python biasanya langsung terlihat di sini. Kalau error muncul saat generate laporan PDF, perhatikan baik-baik — kadang errornya hanya berupa **WARNING**, bukan **ERROR**, jadi gampang terlewat kalau cuma cek exit code. Contoh kasus nyata: laporan PDF berhasil ter-generate (exit code 0) tapi logo dan styling hilang, ternyata di log ada baris:

```
wkhtmltopdf: Exit with code 1 due to network error: ConnectionRefusedError
```

Ini terjadi karena `web.base.url` di database salah mengarah ke port yang dipetakan ke host (misalnya `localhost:18082`), padahal dari *dalam* container, port itu tidak listen di mana-mana — yang benar adalah port internal sesuai `http_port` di `odoo.conf` (misalnya `8082`). Fix-nya:

```bash
docker exec -i odoo19 odoo shell -c /etc/odoo/odoo.conf -d <database> --no-http --log-level=error << 'EOF'
icp = env['ir.config_parameter'].sudo()
icp.set_param('web.base.url', 'http://localhost:8082')
env.cr.commit()
EOF
```

### 2. Masuk ke `odoo shell` untuk Investigasi Cepat

Kalau butuh cek data atau reproduksi bug tanpa lewat UI, `odoo shell` adalah cara tercepat, jalan langsung di dalam container:

```bash
docker exec -it odoo19 odoo shell -c /etc/odoo/odoo.conf -d <database>
```

Dari sini kita punya akses penuh ke `env`, bisa cek record, jalankan method, atau simulasikan akses sebagai user tertentu:

```python
# Reproduksi sebagai user tertentu (misal untuk cek access rights)
env2 = env(user=<user_id>)
record = env2['some.model'].browse(<record_id>)
record.read(['name'])
```

### 3. `breakpoint()` — Cara Termudah untuk Debug Interaktif

Kalau container dijalankan interaktif (foreground, bukan `-d`), kita bisa taruh `breakpoint()` langsung di kode module:

```python
def action_confirm(self):
    result = super().action_confirm()
    breakpoint()   # eksekusi akan berhenti di sini
    return result
```

Trigger action yang memanggil method itu dari UI, lalu attach ke container:

```bash
docker attach odoo19
```

Kita akan mendapat prompt `(Pdb)` persis seperti debugging lokal. Perintah dasar pdb yang sering dipakai:

| Perintah | Fungsi |
|---|---|
| `n` | lanjut ke baris berikutnya |
| `s` | masuk ke dalam function yang dipanggil |
| `c` | lanjutkan eksekusi |
| `p expr` | print isi variable |
| `pp expr` | pretty-print |
| `w` | tampilkan call stack |
| `interact` | masuk ke REPL Python penuh di scope saat ini |

**Catatan:** jangan lupa hapus `breakpoint()` sebelum commit/deploy — cek dengan:

```bash
grep -rn "breakpoint()" /path/to/addons/
```

### 4. Remote Debug dengan `debugpy` (Attach dari IDE)

Kalau butuh debugging visual dengan breakpoint di IDE (VS Code, Cursor), `debugpy` bisa di-attach ke proses Odoo yang berjalan di container.

Install dulu di dalam container (atau tambahkan ke image):

```bash
docker exec odoo19 pip install debugpy
```

Tambahkan snippet berikut di titik yang ingin di-debug (misalnya di `__init__.py` module):

```python
import debugpy
debugpy.listen(("0.0.0.0", 5678))
print("debugpy listening on 5678, menunggu koneksi...")
debugpy.wait_for_client()
```

Expose port debugpy di `docker-compose.yml`:

```yaml
  odoo:
    ports:
      - "8069:8069"
      - "5678:5678"
```

Restart container, lalu attach dari IDE dengan konfigurasi `launch.json`:

```json
{
  "name": "Attach ke Odoo Docker",
  "type": "debugpy",
  "request": "attach",
  "connect": { "host": "localhost", "port": 5678 },
  "pathMappings": [
    { "localRoot": "${workspaceFolder}/addons", "remoteRoot": "/mnt/extra-addons" }
  ]
}
```

Setelah attach, breakpoint yang dipasang di IDE akan aktif dan proses akan berhenti tepat di titik tersebut.

### 5. Pitfall Umum saat Debug di Docker

- **Volume vs bind mount** — kalau folder addons di-mount lewat named volume (bukan bind mount langsung ke host), perubahan kode di host tidak otomatis masuk ke container. Harus di-sync ulang (`docker cp` atau tar-pipe) sebelum debug, kalau tidak kita akan menelusuri kode versi lama.
- **Module belum di-upgrade** — setelah ubah kode Python, container perlu di-restart. Kalau ubah field/view, module juga perlu di-upgrade (`-u nama_module --stop-after-init`) supaya perubahan schema/tampilan benar-benar aktif.
- **Warning vs Error di log** — seperti contoh `wkhtmltopdf` di atas, tidak semua kegagalan menghasilkan exit code non-zero. Selalu scan log secara utuh, bukan cuma cek "sukses/gagal".
- **Port bentrok saat multi-instance** — kalau menjalankan beberapa Odoo sekaligus di `network_mode: host`, pastikan `http_port`, `longpolling_port` unik per instance. Entrypoint image resmi Odoo hanya membaca parameter database dari environment variable, port harus diset lewat config file atau CLI flag.

### Penutup

Debugging Odoo di Docker sebenarnya tidak jauh berbeda dari debugging Python biasa — bedanya kita perlu "masuk" ke dalam container lewat `docker exec`/`docker attach`, atau expose port debugger supaya bisa diakses dari luar. Kombinasi `docker logs` untuk overview cepat, `odoo shell` untuk investigasi data, dan `pdb`/`debugpy` untuk breakpoint interaktif biasanya sudah cukup untuk menyelesaikan sebagian besar kasus.
