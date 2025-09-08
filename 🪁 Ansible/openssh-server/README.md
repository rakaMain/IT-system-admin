# 🔐 Konfigurasi SSH Server — Debian 12 VMware Lab

**Deskripsi singkat:**  
Lab ini memperagakan cara menginstal dan mengkonfigurasi OpenSSH Server di Debian 12 menggunakan VMware, sehingga dapat melakukan remote access dari PC client ke multiple server secara aman melalui protokol SSH dengan autentikasi username/password.

---

## 🔌 Topologi & Alamat

**Network:** `192.168.1.0 / 24`

**Perangkat:**
- **SRV1** (SSH Server) — `192.168.1.1`
- **SRV2** (SSH Server) — `192.168.1.2`  
- **PC** (SSH Client) — `192.168.1.10`
- **Virtual Switch** — menghubungkan semua VM di jaringan VMware

> Semua device menggunakan konfigurasi IP statis dengan subnet mask `255.255.255.0`. Tidak diperlukan gateway untuk komunikasi dalam satu segmen LAN yang sama.

---

## 🔬 Fungsi Lab

- **SSH Server** menyediakan layanan remote shell yang aman dengan enkripsi untuk administrasi jarak jauh.
- **PC Client** dapat mengakses terminal server secara remote menggunakan SSH client.
- **Autentikasi** menggunakan username dan password untuk keamanan akses.
- **Multiple Server Access** memungkinkan client terhubung ke beberapa server dari satu titik.

Singkatnya: **PC Client → SSH connection → Server Terminal → Remote Command Execution**

---

## ✅ Manfaat / Pembelajaran

- 🔐 **Memahami remote administration**: cara mengakses server dari jarak jauh secara aman.
- 🛠️ **Praktik instalasi service Linux**: menginstal dan mengkonfigurasi OpenSSH Server.
- 🌐 **Konfigurasi networking di VMware**: setup virtual network dan IP assignment.
- 🔒 **Keamanan jaringan dasar**: implementasi secure shell untuk remote access.
- 📚 **Dasar system administration** yang essential untuk IT infrastructure management.

---

## ⚙️ Langkah Singkat (Panduan Pelaksanaan)

1. **Setup VM Environment**: buat 3 VM Debian 12 di VMware dengan topologi star.

2. **Update System**: jalankan `sudo apt update && sudo apt upgrade -y` pada semua server.

3. **Install OpenSSH Server**: 
   ```bash
   sudo apt install openssh-server -y
   ```

4. **Konfigurasi IP Statis**: edit `/etc/network/interfaces` sesuai alamat yang ditentukan.

5. **Restart Network Service**: untuk menerapkan konfigurasi IP baru.

6. **Test SSH Connection dari PC**:
   ```bash
   ssh raka@192.168.1.1    # ke SRV1
   ssh raka@192.168.1.2    # ke SRV2
   ```

7. **Verifikasi Remote Access**: pastikan dapat menjalankan perintah di server remote.

---

## 🔍 Expected Result / Verifikasi

- SSH service berjalan di port 22 pada kedua server.
- PC client berhasil login ke SRV1 dan SRV2 menggunakan username `raka`.
- Terminal remote dapat diakses dan perintah Linux dapat dieksekusi.
- Koneksi SSH terenkripsi dan aman dari monitoring jaringan.

---

## 🛟 Troubleshooting (Masalah Umum & Solusi)

- **Connection refused saat SSH?**
  - Pastikan OpenSSH server terinstal dan berjalan: `systemctl status ssh`
  - Periksa firewall tidak memblokir port 22: `sudo ufw allow ssh`

- **Authentication failed?**
  - Verifikasi username dan password benar di server target.
  - Pastikan user account sudah dibuat di server: `sudo adduser raka`

- **Network unreachable?**
  - Periksa konfigurasi IP di `/etc/network/interfaces`
  - Test konektivitas dasar dengan ping antar device.
  - Pastikan virtual network adapter dalam mode yang sama di VMware.

- **Permission denied?**
  - Periksa SSH configuration di `/etc/ssh/sshd_config`
  - Restart SSH service: `sudo systemctl restart ssh`

---

## 📌 Metadata

- **Author:** Raka Kayla (@rkyla_m)
- **Lab:** Konfigurasi OpenSSH Server  
- **Platform:** Debian 12 + VMware Workstation
- **Tanggal:** 2025-09-08