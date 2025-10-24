# 🔐  Ad‑hoc Command Ansible

**Deskripsi singkat:**
Modul ini membahas penggunaan *ad‑hoc commands* Ansible untuk mengelola beberapa host secara cepat (ping, gather_facts, apt, dsb.). Ini adalah kelanjutan dari lab OpenSSH & SSH‑key (SRV1, SRV2, PC) — topologi sekarang memakai **NAT** agar semua VM punya akses internet.

---

## 🔌 Topologi & Alamat (NAT — semua VM punya akses Internet)

**Network:** VM menggunakan adaptor NAT (dapat IP dalam subnet NAT milik VMware/VirtualBox) — contoh IP tetap yang kita gunakan di modul: `192.168.1.1`, `192.168.1.2`, `192.168.1.10` (PC). NAT memungkinkan VM mengakses internet melalui host.

**Perangkat:**

* **SRV1** (SSH & target Ansible) — `192.168.1.1`
* **SRV2** (SSH & target Ansible) — `192.168.1.2`
* **PC** (Control Node / Ansible workstation) — `192.168.1.10`

> Catatan: IP bisa berbeda tergantung konfigurasi NAT di host. Gunakan `ip a` di tiap VM untuk memverifikasi alamat.

---

## 🔬 Fungsi Modul Ini

* Menunjukkan cara menulis dan menggunakan file **inventory** sederhana.
* Menjelaskan tujuan file `ansible.cfg` dan contoh pengaturannya.
* Praktik *ad‑hoc commands* dasar: `ping`, `gather_facts`, `apt` (update/instal), `--limit` dan penggunaan `--key-file`.
* Menunjukkan pengertian `--become` / `--ask-become-pass` dan cara melihat log paket di host.

Singkat: **Control Node (PC) → Ansible ad‑hoc → multiple target (SRV1, SRV2)** untuk administrasi cepat.

---

## ✅ Manfaat / Pembelajaran

* Paham peran *inventory*, *ansible.cfg*, dan opsi CLI.
* Bisa cepat cek konektivitas SSH (`ansible ... -m ping`) antar host.
* Mampu menjalankan perintah manajemen paket serempak (apt) dengan eskalasi hak.
* Memahami output Ansible dan tempat log di target.

---

## ⚙️ Persiapan Singkat

1. Pastikan Ansible terinstal di **PC (workstation/control node)**:

   ```bash
   sudo apt update && sudo apt install -y ansible
   ```
2. Siapkan SSH key untuk Ansible (contoh: `~/.ssh/ansible`) dan pastikan public key sudah ada di `~/.ssh/authorized_keys` di SRV1 & SRV2.
3. Buat folder project, file `inventory`, dan `ansible.cfg` di dalamnya.

Contoh struktur:

```
ansible-project/
├─ inventory
└─ ansible.cfg
```

---

## 🧾 Contoh `inventory` (sederhana)

```
[ip add srv1]
[ip add srv2]
```

> Catatan: Inventory bisa diperluas (grup, host_vars, dll.) saat lingkungan tumbuh.

---

## 🔧 Contoh `ansible.cfg` (benar — gunakan `[defaults]`)

```
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible
remote_user = root
host_key_checking = False
forks = 10
```

* `inventory` → file default yang dipakai Ansible
* `private_key_file` → key yang akan dipakai bila tidak override di CLI
* `remote_user` → user SSH default
* `host_key_checking = False` → nonaktifkan strict host key check (opsional, gunakan hati‑hati)

---

## 🚀 Perintah Ad‑hoc Penting & Penjelasan

### 1) Tes koneksi & autentikasi

```bash
ansible all --key-file ~/.ssh/ansible -i inventory -m ping
```

**Tujuan:** cek apakah control node bisa SSH ke semua host di `inventory` dan modul Ansible dapat berjalan.

* `-m ping` = modul Ansible (bukan ICMP); mengembalikan `pong` bila sukses.
* `--key-file` = override key jika tidak pakai `ansible.cfg`.
* `-i inventory` = gunakan file inventory yang ditentukan.

Jika `SUCCESS` → host reachable dan Python interpreter di target tersedia.

---

### 2) Cara singkat setelah `ansible.cfg` benar

```bash
ansible all -m ping
```

Karena `ansible.cfg` punya `inventory` & `private_key_file`, perintah jadi pendek.

---

### 3) Kumpulkan fakta sistem

```bash
ansible all -m setup
# alias: ansible all -m gather_facts  (setup module mengumpulkan facts)
ansible all -m setup --limit 192.168.1.1
```

* `setup`/`gather_facts` = ambil informasi OS, CPU, memori, IP, dsb.
* `--limit` membatasi target agar hanya host tertentu yang diproses.

---

### 4) Mengelola paket apt (Debian/Ubuntu)

```bash
ansible all -m apt -a "update_cache=true" --become --ask-become-pass
ansible all -m apt -a "name=vim-nox state=present" --become --ask-become-pass
ansible all -m apt -a "name=snapd state=latest" --become --ask-become-pass
```

**Arti komponen:**

* `-m apt` → modul apt (manajemen paket)
* `update_cache=true` → jalankan `apt-get update` sebelum tindakan
* `name=` → nama paket
* `state=present|latest` → `present` = terpasang; `latest` = update ke versi terbaru bila tersedia
* `--become` → jalankan dengan eskalasi (sudo)
* `--ask-become-pass` / `-K` → minta password sudo saat eksekusi

**Catatan:** modul `apt` memerlukan Python/pustaka (`python3-apt`) di target; jika tidak ada akan error.

---

## 🛟 Troubleshooting & Tips Praktis

* Jika `UNREACHABLE` → autentikasi SSH gagal (cek key, user, permission, `ansible.cfg`).
* Jika `FAILED` pada modul apt → periksa apakah `python3`/`python3-apt` ada di target.
* Jika `ssh-copy-id` sudah menambahkan key tapi login masih minta password: cek permission `chmod 700 ~/.ssh` & `chmod 600 ~/.ssh/authorized_keys` di target.
* Gunakan `ssh -vvv user@host` untuk debug SSH, dan `ansible -vvv ...` untuk debug Ansible.

---

## 🔍 Melihat log apt di target

* `/var/log/apt/history.log` — riwayat paket yang diinstall/upgrade
* `/var/log/apt/term.log` — output terminal apt
* `/var/log/dpkg.log` — catatan dpkg

Contoh lihat history:

```bash
ssh root@192.168.1.1 'tail -n 80 /var/log/apt/history.log'
```

---

## 📚 Best Practices singkat

* Simpan `ansible.cfg` di root project agar mudah dipakai ulang.
* Jaga izin private key: `chmod 600 ~/.ssh/ansible`.
* Hindari `host_key_checking = False` di produksi tanpa audit.
* Gunakan `cache_valid_time` di modul apt jika sering menjalankan update: mis. `update_cache=true cache_valid_time=3600`.
* Gunakan grup dan host_vars pada inventory saat lingkungan berkembang.

---

## 📌 Metadata

* **Author:** Raka (@rkyla_m)
* **Modul:** Ad‑hoc Command Ansible (NAT / Internet)
* **Platform:** Debian 12 + VMware Workstation (NAT)
* **Tanggal:** 2025-10-10

