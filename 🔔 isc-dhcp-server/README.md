# 🔐 Konfigurasi ISC DHCP Server — Debian 12 VMware Lab

**Deskripsi singkat:**  
Lab ini memperagakan cara menginstal dan mengkonfigurasi **ISC DHCP Server** di Debian 12 yang berjalan di VMware — khususnya supaya interface `ens33` memberikan IP otomatis (DHCP) ke client yang terhubung. Konfigurasi contoh ini menggunakan subnet `192.168.10.0/24` dan rentang IP dinamis yang ditentukan.

---

## 🔌 Topologi & Alamat

**Network:** `192.168.10.0 / 24`

**Perangkat:**

- **SRV-DHCP** (DHCP Server) — `192.168.10.10` (static pada host, optional untuk management)
    
- **Client** (DHCP Client) — menerima IP dari rentang `192.168.10.100 - 192.168.10.200` via `ens33`
    
- **Virtual Switch / LAN** — menghubungkan semua VM di VMware (mode: Host-only / NAT / Bridge sesuai kebutuhan)
    

> Interface yang kita gunakan sebagai server DHCP adalah `ens33` — pastikan adapter virtual VM di VMware terpasang ke network yang sama dengan client.

---

## 🔬 Fungsi Lab

- Menyediakan layanan DHCP untuk jaringan lokal (alamat IP, gateway, DNS, dan opsi lain).
    
- Mempraktikkan instalasi dan konfigurasi layanan jaringan di Debian 12.
    
- Memahami file konfigurasi `dhcpd.conf` dan pengaturan service `isc-dhcp-server`.
    

Singkatnya: **Client → DHCPDISCOVER → SRV-DHCP (ens33) → DHCPACK → Client menerima IP & konfigurasi jaringan**

---

## ✅ Manfaat / Pembelajaran

- 🛠️ Praktik instalasi service `isc-dhcp-server` di Debian.
    
- 🌐 Memahami alur DHCP (discover, offer, request, ack).
    
- 🔧 Konfigurasi interface VM dan pengaturan `INTERFACES` pada service.
    
- 🧰 Troubleshooting masalah lease dan konflik IP.
    

---

## ⚙️ Langkah Singkat (Panduan Pelaksanaan)

1. **Buat VM Debian 12** di VMware dan pastikan interface `ens33` aktif dan terhubung ke virtual switch yang sama dengan client.
    
2. **Update system & install isc-dhcp-server**:
    

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install isc-dhcp-server -y
```

3. **Set interface yang dipakai oleh dhcpd** (file `/etc/default/isc-dhcp-server`):
    

```bash
# /etc/default/isc-dhcp-server
INTERFACESv4="ens33"
INTERFACESv6=""
```

4. **Isi konfigurasi DHCP** di `/etc/dhcp/dhcpd.conf` (contoh di bawah).
    
5. **Restart service** dan enable pada boot:
    

```bash
sudo systemctl enable --now isc-dhcp-server
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
```

6. **Cek leases dan testing client**: jalankan client (VM/PC) dalam network yang sama, setting ke DHCP, dan lihat apakah mendapatkan IP dari rentang.
    

```bash
# Cek lease file
sudo tail -n 50 /var/lib/dhcp/dhcpd.leases

# Monitor service logs
sudo journalctl -u isc-dhcp-server -f
```

---

## 🔍 Expected Result / Verifikasi

- DHCP server berjalan dan listening pada UDP port 67.
    
- Client yang dikonfigurasi ke DHCP mendapatkan IP antara `192.168.10.100` sampai `192.168.10.200`.
    
- Lease tercatat di `/var/lib/dhcp/dhcpd.leases`.
    
- `sudo systemctl status isc-dhcp-server` menunjukkan `active (running)`.
    

---

## 🛟 Troubleshooting (Masalah Umum & Solusi)

- **Service tidak mau start / crash**
    
    - Periksa file konfigurasi (`/etc/dhcp/dhcpd.conf`) untuk kesalahan sintaks.
        
    - Lihat log: `sudo journalctl -u isc-dhcp-server -e`.
        
- **Port conflict / socket already in use**
    
    - Pastikan tidak ada DHCP server lain (mis. `dnsmasq`) berjalan.
        
- **Client tidak mendapat IP**
    
    - Pastikan VM adapter terhubung ke virtual switch yang sama; cek mode NAT/Bridge/Host-only.
        
    - Pastikan `INTERFACESv4` menunjuk ke interface yang benar (`ens33`).
        
    - Cek apakah ada firewall yang memblok UDP 67/68.
        
- **Alamat statis tidak cocok**
    
    - Periksa apakah fixed-address berada di luar rentang DHCP yang didefinisikan atau bentrok dengan IP lain.
        


## 📌 Metadata

- **Author:** Raka
    
- **Lab:** konfigurasi isc-dhcp-server
    
- **Platform:** Debian 12 + VMware Workstation
    
- **Tanggal:** 2025-09-11
    

---

## Lampiran & Catatan

- ISC DHCP sudah mencapai end-of-life dan proyek Kea merupakan pengganti jangka panjang; contoh di sini tetap menggunakan `isc-dhcp-server` untuk tujuan lab dan pembelajaran. Jika ingin versi produksi jangka panjang, pertimbangkan Kea DHCP (ISC).