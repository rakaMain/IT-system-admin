# 🔐 Modul Lanjutan (Revisi): SSH Keygen & ssh-copy-id — **Menggunakan `id_rsa`** (Debian 12 — VMware Lab)

**Deskripsi singkat:**
Modul ini melanjutkan lab SSH sebelumnya — fokus pada pembuatan pasangan kunci SSH (`ssh-keygen`) menggunakan nama/default `id_rsa`, menyalin public key ke server dengan `ssh-copy-id`, serta konfigurasi agar login **passwordless** (key-based). Dokumen ini disusun agar selaras dengan lab OpenSSH Server (SRV1, SRV2, PC) yang sudah kamu buat.

---

## 🔌 Topologi & Alamat (konteks lab)

**Network:** `192.168.1.0/24`
**Perangkat:**

* **SRV1** (SSH Server) — `192.168.1.1`
* **SRV2** (SSH Server) — `192.168.1.2`
* **PC** (SSH Client) — `192.168.1.10`

> Asumsi: OpenSSH Server sudah terpasang di SRV1 & SRV2. Semua IP statis.

---

## 🔬 Fungsi Modul Ini

* Membuat pasangan kunci di PC client **menggunakan `id_rsa`**.
* Menyalin `id_rsa.pub` ke `~/.ssh/authorized_keys` di server (SRV1 & SRV2).
* Mengatur client agar selalu memakai `id_rsa` untuk host tertentu.
* Troubleshoot kenapa masih minta password walau key sudah dikirim.

Singkat: **PC (private key `~/.ssh/id_rsa`) ⇄ Server (public key in `authorized_keys`) ⇒ Login tanpa password.**

---

## ✅ Manfaat / Pembelajaran

* Memahami autentikasi public-key dengan key bernama/default `id_rsa`.
* Praktik `ssh-keygen`, `ssh-copy-id`, `~/.ssh/config`, dan debugging SSH.
* Menjaga permission yang benar agar `authorized_keys` terbaca.

---

## ⚙️ Langkah Lengkap (Step-by-step)

> Kita gunakan nama default `id_rsa` (RSA 4096 bit) sesuai permintaanmu.

### 1) Siapkan folder `.ssh` di client (PC)

```bash
# di PC (user: raka)
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

### 2) Buat pasangan kunci `id_rsa` 🔑

Jika belum punya atau ingin regenerate:

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "raka@pc"
```

* `-f ~/.ssh/id_rsa` memastikan nama file private = `id_rsa` dan public = `id_rsa.pub`.
* Isi passphrase jika mau (opsional).

Jika sudah punya `~/.ssh/id_rsa` dan ingin tetap pakai yang ada — lewati langkah ini.

### 3) Verifikasi public key

```bash
cat ~/.ssh/id_rsa.pub
ssh-keygen -lf ~/.ssh/id_rsa.pub
```

### 4) Salin public key ke server (gunakan path absolut)

```bash
ssh-copy-id -i /home/raka/.ssh/id_rsa.pub root@192.168.1.1
ssh-copy-id -i /home/raka/.ssh/id_rsa.pub root@192.168.1.2
```

> Gunakan path absolut agar tidak salah `~` (terutama jika menjalankan sebagai root atau sudo).

Jika `ssh-copy-id` berkata `All keys were skipped because they already exist` → artinya key sudah ada di `authorized_keys`.

### 5) Perbaiki permission di server (sangat penting)

Masuk ke server (sekarang masih pakai password) lalu:

```bash
# di SRV1 / SRV2 (sebagai root)
chmod 700 /root/.ssh
chmod 600 /root/.ssh/authorized_keys
chown root:root /root/.ssh /root/.ssh/authorized_keys
```

Jika kamu memasang key untuk user non-root, ganti `/root` ke `/home/username`.

### 6) Pastikan `sshd_config` mengizinkan publickey

Buka `/etc/ssh/sshd_config` pada server:

```
PubkeyAuthentication yes
AuthorizedKeysFile %h/.ssh/authorized_keys
# Untuk root login lewat key:
PermitRootLogin prohibit-password    # atau 'yes' jika memang ingin, jangan 'no'
```

Restart SSH jika ada perubahan:

```bash
sudo systemctl restart ssh
```

### 7) Memaksa client gunakan `id_rsa` (opsional tapi direkomendasikan)

Satu kali:

```bash
ssh -i ~/.ssh/id_rsa root@192.168.1.1
```

Supaya otomatis untuk host tertentu, tambahkan di `~/.ssh/config`:

```text
Host srv1
    HostName 192.168.1.1
    User root
    IdentityFile /home/raka/.ssh/id_rsa
    IdentitiesOnly yes

Host srv2
    HostName 192.168.1.2
    User root
    IdentityFile /home/raka/.ssh/id_rsa
    IdentitiesOnly yes
```

Simpan lalu:

```bash
chmod 600 ~/.ssh/config
ssh srv1
ssh srv2
```

`IdentitiesOnly yes` mencegah SSH mencoba banyak key lain dan memastikan `id_rsa` yang dipakai.

### 8) (Opsional) Gunakan `ssh-agent` untuk passphrase

Jika private key punya passphrase dan kamu tidak ingin mengetik tiap kali:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

---

## 🔍 Expected Result / Verifikasi

* `ssh srv1` (atau `ssh -i ~/.ssh/id_rsa root@192.168.1.1`) langsung login tanpa prompt password.
* `ssh -v root@192.168.1.1` menunjukkan `Offering public key: /home/raka/.ssh/id_rsa` dan `Authentication succeeded (publickey)`.
* `cat /root/.ssh/authorized_keys` di server berisi baris panjang yang sama dengan `~/.ssh/id_rsa.pub`.

---

## 🛟 Troubleshooting (Masalah Umum & Solusi)

### Masalah: `ssh-copy-id` bilang key masuk, tapi `ssh` masih minta password

* Periksa permission: `chmod 700 ~/.ssh` & `chmod 600 ~/.ssh/authorized_keys` di **server**.
* Pastikan kamu login user yang **sama** dengan tempat key ditaruh (root vs non-root).
* Periksa isi `authorized_keys` tidak terpotong/terformat salah (CRLF). Gunakan `dos2unix` jika perlu.
* Jalankan debug dari client:

  ```bash
  ssh -vvv -i ~/.ssh/id_rsa root@192.168.1.1
  ```

  * Cari `Offering public key: /home/.../id_rsa` → jika tidak muncul, ssh tidak menggunakan key itu.
  * Jika muncul tapi ada `bad ownership or modes` → perbaiki permission di server.

### Masalah: `ssh-copy-id` menambahkan `id_rsa` padahal kamu ingin key lain

* Kita sekarang memang pakai `id_rsa` — itu normal. Jika kamu ingin pakai key lain, selalu pakai `-i /path/to/key.pub`.

### Periksa log server jika masih gagal

```bash
# di server
sudo tail -n 200 /var/log/auth.log
# atau
sudo journalctl -u ssh -e
```

Cari pesan `publickey` atau `Authentication refused`.

---

## ⚠️ Best Practices Singkat

* Jangan bagikan `~/.ssh/id_rsa` (private key).
* Backup private key dengan aman.
* Pertimbangkan `ed25519` untuk kunci baru (lebih singkat & aman) — tapi jika kamu ingin tetap `id_rsa`, gunakan 4096 bit.
* Gunakan `~/.ssh/config` dan `IdentitiesOnly yes` untuk kontrol key yang dipakai per-host.

---

## 📌 Metadata

* **Author:** Raka (@rkyla_m)
* **Modul:** SSH Keygen & ssh-copy-id (menggunakan `id_rsa`)
* **Platform:** Debian 12 + VMware Workstation
* **Tanggal:** 2025-10-10

---