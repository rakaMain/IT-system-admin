
# ⚙️ Modul Lanjutan (Revisi): **Ansible Ad-Hoc Command** — _Menggunakan `id_ansible` (Debian 12 — VMware Lab)_

---

## 🧭 Deskripsi Singkat

Modul ini melanjutkan lab sebelumnya (SSH Keygen & ssh-copy-id menggunakan `id_rsa`) dan kini berfokus pada **otomatisasi server menggunakan Ansible** melalui **Ad-Hoc Command**.  
Praktik ini menggunakan kunci SSH khusus bernama `id_ansible`, agar Ansible dapat terhubung ke server tanpa password melalui koneksi SSH otomatis.

---

## 🔌 Topologi & Alamat (Konteks Lab)

**Mode Jaringan:** NAT (DHCP otomatis dari VMware)

**Alamat IP:**

- **PC (Ansible Controller)** — `192.168.179.151`
    
- **SRV1 (Managed Host 1)** — `192.168.179.152`
    
- **SRV2 (Managed Host 2)** — `192.168.179.148`
    

> Semua perangkat menggunakan Debian 12 dan sudah terpasang OpenSSH Server.  
> Ansible dijalankan di PC (controller), sedangkan SRV1 dan SRV2 sebagai node yang dikendalikan.

---

## 🎯 Fungsi Modul Ini

- Membuat pasangan kunci SSH baru dengan nama `id_ansible`.
    
- Menyalin kunci publik ke SRV1 dan SRV2 untuk koneksi tanpa password.
    
- Mengatur konfigurasi dasar Ansible (`inventory` dan `ansible.cfg`).
    
- Menjalankan **Ad-Hoc Command** untuk otomasi perintah server sederhana.
    

---

## ✅ Tujuan Pembelajaran

- Memahami konsep dasar **Ansible Ad-Hoc Command**.
    
- Menggunakan **key-based authentication (`id_ansible`)** dalam automasi.
    
- Mengontrol banyak server sekaligus hanya dengan satu perintah.
    
- Menguasai sintaks dasar modul Ansible seperti `ping`, `apt`, dan `gather_facts`.
    

---

## 🧰 Langkah-langkah Praktikum

### 1) Buat SSH Key Baru (`id_ansible`)

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_ansible -C "raka@ansible"
```

> File akan tersimpan di `~/.ssh/id_ansible` (private) dan `~/.ssh/id_ansible.pub` (public).

---

### 2) Kirim Public Key ke Server Target

```bash
ssh-copy-id -i ~/.ssh/id_ansible.pub root@192.168.179.152
ssh-copy-id -i ~/.ssh/id_ansible.pub root@192.168.179.148
```

> Gunakan path absolut jika muncul error `Permission denied` atau key tidak terbaca.

---

### 3) Pastikan Permission Tepat di Server

Masuk ke tiap server dan jalankan:

```bash
chmod 700 /root/.ssh
chmod 600 /root/.ssh/authorized_keys
chown root:root /root/.ssh /root/.ssh/authorized_keys
```

> Permission yang salah akan membuat Ansible gagal login dengan public key.

---

### 4) Instal Ansible di PC (Controller)

```bash
sudo apt install ansible -y
```

---

### 5) Buat Folder & File Dasar

```bash
mkdir ~/ansible-tutorial
cd ~/ansible-tutorial
nano inventory
```

Isi file `inventory`:

```
[dbserver]
192.168.179.152
192.168.179.148
```

> File ini berisi daftar host/server yang akan dikendalikan Ansible.

---

### 6) Buat File Konfigurasi `ansible.cfg`

```bash
nano ansible.cfg
```

Isi file:

```
[defaults]
inventory = inventory
private_key_file = ~/.ssh/id_ansible
```

> Pastikan path menuju private key (`id_ansible`) benar.

---

### 7) Uji Koneksi SSH & Autentikasi Ansible

```bash
ansible all --key-file ~/.ssh/id_ansible -i inventory -m ping
```

> Jika koneksi berhasil, output akan menampilkan `pong` untuk setiap host.

---

## ⚙️ Menjalankan Ad-Hoc Command

### 🔹 a. Mengumpulkan Informasi Sistem (gather_facts)

```bash
ansible all -m gather_facts
```

> Menampilkan detail OS, IP, CPU, memori, dan informasi sistem tiap host.

---

### 🔹 b. Update Repository Paket

```bash
ansible all -m apt -a "update_cache=true"
```

> Sama seperti menjalankan `apt update` di tiap server.

---

### 🔹 c. Install Paket di Semua Server

```bash
ansible all -m apt -a "name=bind9 state=present update_cache=true"
```

> Gunakan `state=latest` jika ingin memastikan versi terbaru terpasang.

---

### 🔹 d. Hapus Paket

```bash
ansible all -m apt -a "name=bind9 state=absent"
```

---

### 🔹 e. Hapus Paket Sekaligus Konfigurasinya

```bash
ansible all -m apt -a "name=bind9 state=absent purge=yes"
```

---

## 🧩 Analogi Sederhana

|Konsep|Analogi|
|---|---|
|**Ad-Hoc Command**|Seperti _kepala koki_ yang memberi perintah langsung ke para koki: “Masak nasi sekarang!” — hanya sekali jalan.|
|**Playbook**|Seperti _buku resep lengkap_ yang bisa dijalankan berulang dengan hasil sama setiap kali.|

---

## 🧠 Troubleshooting

**Masalah:** `Permission denied (publickey,password)`  
**Solusi:**

- Pastikan permission key benar:
    
    ```bash
    chmod 600 ~/.ssh/id_ansible
    ```
    
- Cek apakah public key sudah tersalin ke `/root/.ssh/authorized_keys` di server.
    
- Gunakan mode debug:
    
    ```bash
    ansible all -m ping -vvv
    ```
    

---

## 💡 Tips & Best Practice

- Pisahkan SSH key untuk Ansible (`id_ansible`) agar tidak tercampur dengan key pribadi.
    
- Simpan backup key di lokasi aman.
    
- Gunakan grup host di file `inventory` untuk memudahkan manajemen skala besar.
    
- Gunakan opsi `--limit` untuk menargetkan host tertentu saja.
    

---

## 📌 Metadata

- **Author:** Raka (@rkyla_m)
    
- **Modul:** Ansible Ad-Hoc Command (menggunakan `id_ansible`)
    
- **Platform:** Debian 12 + VMware NAT
    
- **Tanggal:** 2025-11-04
    

---

