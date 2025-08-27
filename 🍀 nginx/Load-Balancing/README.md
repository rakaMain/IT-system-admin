# Nginx JSON Log Format

Deskripsi singkat:

* 📘 Dokumen ini menjelaskan format log JSON untuk Nginx (access log) yang direkomendasikan untuk dipakai pada proyek repository — mudah dibaca mesin, mudah diparse, dan cocok untuk monitoring/pipeline log.

# 🔧 Cara kerja & awalan log

* Setiap request yang diterima Nginx akan menghasilkan satu baris log di file load-access log.
* Pada konfigurasi JSON, kita menyusun `log_format` yang memetakan variabel-variabel Nginx ke field JSON.
* **Awalan log (prefix)** biasanya tidak diperlukan karena setiap baris sudah berupa objek JSON lengkap; namun jika ingin menandai sumber (mis. `env`, `cluster`, atau `instance`) bisa menambahkan field seperti `source` atau `instance_id` di `log_format`.

Contoh `nginx.conf` (potongan) untuk membuat log JSON:

```nginx
log_format json_combined escape=json '{'
  '"time":"$time_local",'
  '"remote_addr":"$remote_addr",'
  '"remote_user":"$remote_user",'
  '"host":"$host",'
  '"request":"$request",'
  '"method":"$request_method",'
  '"uri":"$request_uri",'
  '"status":"$status",'
  '"body_bytes_sent":"$body_bytes_sent",'
  '"request_time":"$request_time",'
  '"upstream_response_time":"$upstream_response_time",'
  '"upstream_addr":"$upstream_addr",'
  '"upstream_status":"$upstream_status",'
  '"http_referer":"$http_referer",'
  '"http_user_agent":"$http_user_agent"'
'}';

access_log  /var/log/nginx/access.log  json_combined;
```

> Catatan:
>
> * Gunakan `escape=json` untuk memastikan tanda kutip dan karakter khusus di-escape dengan benar.
> * Jika ingin menambahkan fields tambahan (mis. `request_id`, `trace_id`, `service_name`), tambahkan langsung pada `log_format`.

# 📄 Dokumentasi field log JSON

* 🕒 **`time` — `$time_local`**

  * Waktu lokal saat request diterima oleh Nginx.
  * Contoh: `"24/Aug/2025:11:05:10 +0700"`.

* 🌐 **`remote_addr` — `$remote_addr`**

  * Alamat IP client yang menghubungi load balancer / Nginx.
  * Contoh: `"10.0.0.5"`.

* 👤 **`remote_user` — `$remote_user`**

  * Nama user jika memakai HTTP Basic Auth; `-` kalau tidak ada.
  * Contoh: `"-"` atau `"raka"`.

* 🏷️ **`host` — `$host`**

  * Host header atau nama domain yang diminta.
  * Contoh: `"example.com"`.

* 📨 **`request` — `$request`**

  * Baris request lengkap (method + path + versi HTTP).
  * Contoh: `"GET /index.html HTTP/1.1"`.

* 🔁 **`method` — `$request_method`**

  * Metode HTTP (GET, POST, PUT, dsb.).
  * Contoh: `"GET"`.

* 🔗 **`uri` — `$request_uri`**

  * Path + query string yang diminta.
  * Contoh: `"/page?a=1"`.

* ✅❌ **`status` — `$status`**

  * Status code yang dikembalikan Nginx ke client (200, 404, 500, ...).
  * Contoh: `"200"`.

* 📦 **`body_bytes_sent` — `$body_bytes_sent`**

  * Jumlah byte body yang dikirim ke client (tidak termasuk header).
  * Contoh: `"1024"`.

* ⏱️ **`request_time` — `$request_time`**

  * Total waktu dari Nginx menerima request sampai selesai mengirim response ke client — berguna untuk melihat latency end-to-end.
  * Contoh: `"0.123"` (detik).

* ⚙️ **`upstream_response_time` — `$upstream_response_time`**

  * Waktu yang dihabiskan backend (upstream) untuk merespons.
  * Bisa `"-"` kalau tidak ada upstream.
  * Jika ada retry/beberapa percobaan → bisa berupa daftar waktu dipisah koma, mis. `"0.010, 0.020"`.

* 🧭 **`upstream_addr` — `$upstream_addr`** ← **(penting — menunjukkan server upstream yang dipakai)**

  * Alamat IP\:port backend yang menerima request dari Nginx.
  * Satu percobaan sukses: `"192.168.1.11:80"`.
  * Beberapa percobaan/retry: `"192.168.1.11:80, 192.168.1.12:80"`.
  * Bukan proxy / upstream tidak tersedia: `"-"`.

* 🧾 **`upstream_status` — `$upstream_status`**

  * HTTP status code yang dikembalikan backend (bisa berisi daftar bila multiple attempts).
  * Contoh: `"200"` atau `"502, 200"`.

* 🔗 **`http_referer` — `$http_referer`**

  * Header Referer — halaman asal yang menautkan request (atau `-`).
  * Contoh: `"-"` atau `"https://google.com"`.

* 🧑‍💻 **`http_user_agent` — `$http_user_agent`**

  * Header User-Agent — info browser atau client.
  * Contoh: `"curl/7.68.0"` atau `"Mozilla/5.0 (...)"`.

# ⚠️ Best practice singkat

* Jangan menyimpan data sensitif (mis. token, password) secara mentah di log.
* Pastikan format waktu konsisten bila butuh analisis historis (pertimbangkan UTC atau format ISO saat perlu interoperability).
* Tambahkan `request_id` untuk mempermudah korelasi request di berbagai sistem.

# 📌 Contoh hasil `logging`

```json
{"time":"24/Aug/2025:11:05:10 +0700","remote_addr":"10.0.0.5","remote_user":"-","host":"example.com","request":"GET /index.html HTTP/1.1","method":"GET","uri":"/index.html","status":"200","body_bytes_sent":"1024","request_time":"0.123","upstream_response_time":"0.045","upstream_addr":"192.168.1.11:80","upstream_status":"200","http_referer":"-","http_user_agent":"curl/7.68.0"}
```

---
