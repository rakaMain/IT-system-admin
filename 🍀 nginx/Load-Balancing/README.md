##### Dokumentasi field log JSON 

- 🕒 **`time` — `$time_local`**  
    Waktu lokal saat request diterima oleh Nginx.  
    Contoh: `"24/Aug/2025:11:05:10 +0700"`.


- 🌐 **`remote_addr` — `$remote_addr`**  
    Alamat IP client yang menghubungi load balancer.  
    Contoh: `"10.0.0.5"`.
    
- 👤 **`remote_user` — `$remote_user`**  
    Nama user jika memakai HTTP Basic Auth; `-` kalau tidak ada.  
    Contoh: `"-"` atau `"raka"`.
    
- 🏷️ **`host` — `$host`**  
    Host header atau nama domain yang diminta.  
    Contoh: `"example.com"`.
    
- 📨 **`request` — `$request`**  
    Baris request lengkap (method + path + versi HTTP).  
    Contoh: `"GET /index.html HTTP/1.1"`.
    
- 🔁 **`method` — `$request_method`**  
    Metode HTTP (GET, POST, PUT, dsb.).  
    Contoh: `"GET"`.
    
- 🔗 **`uri` — `$request_uri`**  
    Path + query string yang diminta.  
    Contoh: `"/page?a=1"`.
    
- ✅❌ **`status` — `$status`**  
    Status code yang dikembalikan Nginx ke client (200, 404, 500, ...).  
    Contoh: `"200"`.
    
- 📦 **`body_bytes_sent` — `$body_bytes_sent`**  
    Jumlah byte body yang dikirim ke client (tidak termasuk header).  
    Contoh: `"1024"`.
    
- ⏱️ **`request_time` — `$request_time`**  
    Total waktu dari Nginx menerima request sampai selesai mengirim response ke client — bagus untuk melihat latency end-to-end.  
    Contoh: `"0.123"` (detik).
    
- ⚙️ **`upstream_response_time` — `$upstream_response_time`**  
    Waktu yang dihabiskan backend (upstream) untuk merespons.
    
    - Bisa `"-"` kalau tidak ada upstream.
        
    - Jika ada retry/beberapa percobaan → bisa berupa daftar waktu dipisah koma, mis. `"0.010, 0.020"`.
        
- 🧭 **`upstream_addr` — `$upstream_addr`** ← **(penting — menunjukkan server upstream yang dipakai)**  
    Alamat IP:port backend yang menerima request dari Nginx.
    
    - Satu percobaan sukses: `"192.168.1.11:80"`.
        
    - Beberapa percobaan/retry: `"192.168.1.11:80, 192.168.1.12:80"`.
        
    - Bukan proxy / upstream tidak tersedia: `"-"`.
        
    
- 🧾 **`upstream_status` — `$upstream_status`**  
    HTTP status code yang dikembalikan backend (bisa berisi daftar bila multiple attempts).  
    Contoh: `"200"` atau `"502, 200"`.
    
- 🔗 **`http_referer` — `$http_referer`**  
    Header Referer — halaman asal yang menautkan request (atau `-`).  
    Contoh: `"-"` atau `"https://google.com"`.
    
- 🧑‍💻 **`http_user_agent` — `$http_user_agent`**  
    Header User-Agent — info browser atau client.  
    Contoh: `"curl/7.68.0"` atau `"Mozilla/5.0 (...)"`.
    