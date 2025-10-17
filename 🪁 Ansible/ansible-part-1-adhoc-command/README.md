Bagus — saya jelaskan singkat, jelas, dan langsung ke poin untuk tiap pertanyaan kamu. Saya tambahkan contoh kecil dan catatan praktis. 🚀

# 1) Apa gunanya **file `inventory`**?

* **Fungsi:** menyimpan daftar host/target (IP atau hostname) yang akan dikelola Ansible.
* **Kenapa isinya seperti itu (`192.168.1.1`, `192.168.1.2`)?** karena format paling sederhana adalah satu host per baris — Ansible cukup baca daftar itu untuk tahu ke mana harus SSH.
* **Bisa lebih dari itu:** kamu bisa buat grup, variabel host, file YAML, atau struktur lebih kompleks. Contoh sederhana (INI style):

  ```
  webservers
  192.168.1.1
  192.168.1.2
  ```
* **Intinya:** inventory adalah “daftar alamat” yang menjadi target tugas otomatisasi.

# 2) Apa gunanya command

`ansible all --key-file ~/.ssh/ansible -i inventory -m ping` ? 🤔

* **Tujuan:** *menguji koneksi dan otentikasi SSH* dari mesin kontrol ke semua host yang ada di file `inventory`.
* **Penjelasan tiap bagian:**

  * `ansible all` → jalankan ke semua host di inventory.
  * `--key-file ~/.ssh/ansible` → pakai private key SSH tersebut untuk autentikasi (alternatif ke password).
  * `-i inventory` → gunakan file inventory yang disebut `inventory`.
  * `-m ping` → gunakan modul Ansible bernama `ping` (module ini menjalankan kecil skrip lewat SSH, bukan ICMP).
* **Hasil yang diharapkan:** setiap host yang berhasil konek mengembalikan `pong` (contoh: `192.168.1.1 | SUCCESS => { "changed": false, "ping": "pong" }`).
* **Catatan penting:** modul `ping` membutuhkan akses SSH dan interpreter (Python) di host target — bukan ICMP network ping.

# 3) Apa gunanya **file `ansible.cfg`** dan kenapa isinya seperti itu?

Contoh yang kamu tulis:

```
[default]
inventory = inventory
private_key_file = ~/.ssh/ansible
```

* **Fungsi `ansible.cfg`:** konfigurasi default untuk Ansible sehingga kamu tidak perlu menulis opsi panjang di tiap perintah.
* **Mengapa setting itu berguna:**

  * `inventory = inventory` → Ansible otomatis pakai file `inventory` tanpa perlu `-i inventory` di command.
  * `private_key_file = ~/.ssh/ansible` → Ansible otomatis pakai key itu untuk autentikasi, jadi tidak perlu `--key-file` setiap kali.
* **Manfaat praktis:** perintah jadi lebih singkat dan konsisten (`ansible all -m ping` cukup).
* **Catatan keamanan:** pastikan file key punya permission yang aman (mis. `600`) agar SSH tidak menolak.

# 4) Apa gunanya perintah-perintah ini?

## `ansible all -m ping`

* **Fungsi:** tes koneksi cepat ke semua host (menggunakan inventory & key dari `ansible.cfg` bila tersedia).
* **Perbedaan dengan `ping` jaringan:** bukan ICMP — Ansible menjalankan modul Python kecil di remote dan mengembalikan `pong`.

## `ansible all -m gather_facts` (atau ad-hoc module `setup`)

* **Fungsi:** kumpulkan *fakta* tentang sistem target — informasi seperti OS, IP, memori, CPU, path, dsb.
* **Catatan:** dalam playbook kata kunci `gather_facts: yes` sering dipakai; untuk ad-hoc biasanya module `setup` dipanggil: `ansible all -m setup`. Hasilnya adalah JSON besar berisi banyak variabel yang bisa dipakai di playbook.

## `ansible all -m gather_facts --limit 192.168.1.1`

* **Fungsi `--limit`:** batasi target hanya ke host (atau grup) tertentu. Di contoh ini hanya `192.168.1.1` yang dikumpulkan faktanya. Berguna saat ingin tes atau jalankan tugas hanya pada subset host.

# 5) Ringkasan praktis & tips cepat ✅

* Jika sudah punya `ansible.cfg` yang benar, cukup pakai: `ansible all -m ping`.
* `ping` module = tes koneksi & autentikasi SSH (butuh Python di host).
* `setup` / `gather_facts` = ambil info hardware/software host.
* `--limit` = batasi target.
* Gunakan inventory lebih terstruktur (grup, host_vars) bila lingkungan berkembang.
* Pastikan key SSH dan user yang dipakai punya izin SSH ke target.

Kalau mau, saya bisa:

* ubah penjelasan ini ke versi slide singkat untuk presentasi, atau
* berikan contoh `inventory` yang berisi grup + host_vars, atau
* tunjukkan contoh output `ansible all -m setup` yang disederhanakan.

---

Singkat & jelas — penjelasan tentang `default` di `ansible.cfg` (dengan koreksi kecil):

1. **Yang benar adalah `[defaults]`**, bukan `[default]`.

   * Seksi header di file `ansible.cfg` dinamai **`[defaults]`**. Jika kamu pakai `[default]`, Ansible **mengabaikannya** karena tidak cocok nama sectornya.

2. **Apa fungsi `[defaults]`?**

   * Itu adalah tempat kamu menaruh pengaturan bawaan (default) untuk perilaku Ansible — mis. file `inventory` yang dipakai, kunci privat, user remote, timeout, dsb. Pengaturan di sini membuat perintah jadi lebih singkat karena tidak perlu menambahkan opsi tiap kali.

3. **Contoh ringkas isi `[defaults]`:**

   ```
   [defaults]
   inventory = inventory
   private_key_file = ~/.ssh/ansible
   remote_user = ubuntu
   host_key_checking = False   # (opsional — nonaktifkan strict host key checking)
   forks = 10
   ```

4. **Beberapa opsi umum di `[defaults]` dan gunanya singkat:**

   * `inventory` → path ke file inventory default.
   * `private_key_file` → path ke SSH private key default.
   * `remote_user` → user SSH yang dipakai jika tidak diberi di command.
   * `host_key_checking` → matikan/aktifkan pengecekan fingerprint host (keamanan vs kenyamanan).
   * `forks` → berapa banyak koneksi paralel (skala eksekusi).

5. **Prioritas konfigurasi (singkat):**

   * Opsi yang kamu tulis **di command line** (mis. `--key-file`) punya prioritas tertinggi.
   * Lalu environment variables.
   * Untuk file config, urut prioritas: file yang ditunjuk `ANSIBLE_CONFIG` → `./ansible.cfg` (direktori kerja saat ini) → `~/.ansible.cfg` → `/etc/ansible/ansible.cfg`.

6. **Tips praktis:**

   * Gunakan `[defaults]` dan simpan `ansible.cfg` di folder project supaya mudah.
   * Pastikan permission private key `~/.ssh/ansible` aman (`chmod 600`).
   * Jangan gunakan `host_key_checking = False` di produksi tanpa pertimbangan keamanan.

Mau saya perbaiki file `ansible.cfg` kamu jadi format yang benar dan rapi sesuai contoh di atas? 😊
