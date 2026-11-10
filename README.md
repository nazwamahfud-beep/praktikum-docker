# LAPORAN PRAKTIKUM CLOUD COMPUTING
## DOCKER DASAR – PERTEMUAN 4

### Identitas Mahasiswa

- **Nama:** Nazwa Mahfud
- **NIM:** 24231005
- **Program Studi:** Sistem Informasi
- **Fakultas:** Sains dan Teknologi
- **Universitas:** Universitas Respati Yogyakarta
- **Mata Kuliah:** Cloud Computing
- **Platform Praktikum:** KillerCoda – Ubuntu Playground

---

## 1. Pemeriksaan Docker dan Sistem Operasi Host

Pada langkah pertama dilakukan pemeriksaan sistem operasi dan versi Docker yang digunakan dalam lingkungan praktikum KillerCoda.

Perintah yang digunakan:

```bash
cat /etc/os-release
docker version
docker info | head -20
```

Hasil pemeriksaan menunjukkan bahwa sistem operasi host yang digunakan adalah Ubuntu 24.04.

- Sistem Operasi Host: Ubuntu 24.04
- Versi Docker Engine: [Isi sesuai hasil docker version]

Docker digunakan sebagai platform untuk menjalankan aplikasi dalam container tanpa harus melakukan instalasi aplikasi secara manual pada sistem operasi host.

## 2. Pemeriksaan Container Alpine dan Host Ubuntu

Pada langkah ini dilakukan pengujian untuk mengetahui perbedaan sistem operasi container dengan sistem operasi host.

Perintah yang digunakan pada host Ubuntu:

```bash
cat /etc/os-release
uname -r
```

Kemudian menjalankan container Alpine:

```bash
docker run -it --rm alpine sh
```

Di dalam container Alpine dijalankan perintah:

```bash
cat /etc/os-release
uname -r
ls /
exit
```

### Hasil Pengamatan

1. Host menggunakan sistem operasi Ubuntu 24.04.
2. Container menggunakan sistem operasi Alpine Linux.
3. Perintah `uname -r` menunjukkan versi kernel yang sama antara container dan host.

Hal tersebut menunjukkan bahwa container dapat menggunakan lingkungan sistem operasi yang berbeda, tetapi tetap menggunakan kernel milik host. Berbeda dengan Virtual Machine (VM) yang memiliki kernel sendiri.

## 3. Menjalankan Web Server Nginx

Pada langkah ini dilakukan pembuatan container Nginx menggunakan perintah:

```bash
docker run -d --name web -p 8080:80 nginx
```

Keterangan:
- `docker run` digunakan untuk membuat dan menjalankan container.
- `-d` menjalankan container di latar belakang.
- `--name web` memberikan nama container yaitu web.
- `-p 8080:80` menghubungkan port 8080 pada host dengan port 80 pada container.
- `nginx` merupakan image yang digunakan.

Selanjutnya dilakukan pemeriksaan container:

```bash
docker ps
curl -I localhost:8080
```

Pengujian dilakukan melalui fitur Traffic / Ports pada KillerCoda dengan memasukkan port 8080.

## 4. Mengubah Halaman Web Nginx

Pada langkah ini halaman bawaan Nginx diubah menjadi halaman yang menampilkan identitas mahasiswa.

Perintah yang digunakan:

```bash
docker exec web sh -c 'echo "<h1>Praktikum Cloud Computing - NIM 24231005</h1>" > /usr/share/nginx/html/index.html'
```

Untuk memeriksa hasil perubahan digunakan perintah:

```bash
curl localhost:8080
```

Hasil pengujian menampilkan tulisan:

**Praktikum Cloud Computing - NIM 24231005**

Folder website pada image resmi Nginx adalah `/usr/share/nginx/html`.

### Screenshot Pengujian Nginx

[Masukkan screenshot halaman Nginx melalui Traffic / Ports dengan port 8080. Pastikan URL dan NIM terlihat.]

## 5. Melihat Log dan Penggunaan Sumber Daya Container

Pada langkah ini dilakukan pemeriksaan aktivitas dan penggunaan sumber daya container.

Perintah yang digunakan:

```bash
docker logs web
docker logs --tail 5 web
docker stats --no-stream
docker inspect web | grep -i ipaddress
```

Keterangan:
- `docker logs web` digunakan untuk melihat log aktivitas container.
- `docker logs --tail 5 web` digunakan untuk menampilkan lima baris log terakhir.
- `docker stats --no-stream` digunakan untuk melihat penggunaan CPU dan memori container.
- `docker inspect web` digunakan untuk melihat informasi detail container, termasuk alamat IP internal.

## 6. Pengujian Siklus Hidup Container

Pada langkah ini dilakukan pengujian untuk mengetahui perbedaan antara menghentikan container dan menghapus container.

### A. Pengujian Stop dan Start

Perintah yang digunakan:

```bash
docker stop web
docker ps
docker ps -a
docker start web
curl localhost:8080
```

Hasil pengamatan:

Setelah container dihentikan menggunakan `docker stop`, container tidak lagi berjalan, tetapi masih tersimpan. Ketika dijalankan kembali menggunakan `docker start`, halaman web tetap menampilkan tulisan NIM yang sebelumnya telah dibuat.

Hal tersebut terjadi karena container yang digunakan masih sama sehingga perubahan di dalamnya tetap tersimpan.

### B. Pengujian Remove dan Run

Perintah yang digunakan:

```bash
docker rm -f web
docker run -d --name web -p 8080:80 nginx
curl localhost:8080
```

Hasil pengamatan:

Setelah container lama dihapus menggunakan `docker rm`, kemudian dibuat kembali menggunakan `docker run`, halaman web kembali menampilkan halaman bawaan Nginx yaitu "Welcome to nginx!".

Hal tersebut terjadi karena container baru dibuat dari image asli yang belum mengalami perubahan. Perubahan pada container sebelumnya tidak tersimpan secara permanen di dalam image.

## 7. Menjalankan Beberapa Container Nginx

Pada langkah ini dilakukan pengujian dengan menjalankan beberapa container Nginx secara bersamaan menggunakan image yang berbeda.

Perintah yang digunakan:

```bash
docker run -d --name web-alpine -p 8081:80 nginx:alpine
docker run -d --name web-lama -p 8082:80 nginx:1.26
docker ps
docker images
```

Kemudian dilakukan pemeriksaan menggunakan perintah:

```bash
curl -sI localhost:8081 | grep Server
curl -sI localhost:8082 | grep Server
```

### Tabel Ukuran Image Docker

Berdasarkan tampilan `docker images` pada praktikum:

| Nama Image | Ukuran |
|---|---:|
| nginx:latest | 192 MB |
| nginx:alpine | 63 MB |
| nginx:1.26 | 192 MB |

Keterangan:

Ukuran image dapat berbeda karena sistem operasi dasar dan komponen yang disertakan dalam setiap image tidak sama. Image Nginx Alpine memiliki ukuran lebih kecil karena menggunakan Alpine Linux yang ringan.

## 8. Jawaban Pertanyaan Evaluasi

### 1. Container Alpine berjalan di host Ubuntu, tetapi `uname -r` keduanya sama. Apa yang dibuktikan hasil ini tentang perbedaan container dan VM?

Hasil tersebut membuktikan bahwa container tidak memiliki kernel sendiri seperti Virtual Machine (VM). Container Alpine dapat menggunakan sistem operasi yang berbeda dari host Ubuntu, tetapi tetap menggunakan kernel milik host. Sedangkan VM memiliki sistem operasi dan kernel sendiri. Oleh karena itu, container lebih ringan dan membutuhkan waktu lebih cepat untuk dijalankan dibandingkan VM.

### 2. Apa perbedaan image dan container? Gunakan contoh dari Langkah 8, ketika satu image Nginx dapat menjadi beberapa container.

Image adalah template atau cetakan yang berisi aplikasi dan kebutuhan untuk menjalankannya, sedangkan container adalah image yang sudah dijalankan. Contohnya pada praktikum, image Nginx digunakan untuk membuat beberapa container, yaitu `web`, `web-alpine`, dan `web-lama`. Ketiganya dapat berjalan secara bersamaan menggunakan image atau versi Nginx yang berbeda.

### 3. Pada perintah `docker run -p 8081:80 nginx:alpine`, angka mana yang merupakan port host dan mana port container? Mengapa ketiga container Nginx boleh sama-sama memakai port 80 di dalam container?

Angka 8081 merupakan port host, sedangkan angka 80 merupakan port container. Format penulisan port mapping adalah port host diikuti port container. Ketiga container Nginx dapat menggunakan port 80 secara bersamaan karena setiap container memiliki lingkungan jaringan yang terisolasi. Sementara itu, pada host digunakan port yang berbeda, yaitu 8080, 8081, dan 8082 agar tidak terjadi konflik.

### 4. Mengapa perubahan halaman di Langkah 5 hilang setelah `docker rm` lalu `docker run`, tetapi tidak hilang setelah `docker stop` lalu `docker start`?

Perubahan halaman tidak hilang setelah `docker stop` dan `docker start` karena container yang digunakan masih sama. Perintah stop hanya menghentikan container sementara, sedangkan start menjalankan kembali container tersebut sehingga perubahan yang sebelumnya dibuat tetap tersimpan.

Namun, setelah menggunakan `docker rm`, container lama dihapus. Ketika menjalankan `docker run` kembali, Docker membuat container baru dari image asli Nginx. Karena perubahan sebelumnya hanya tersimpan di container lama dan tidak di dalam image, halaman kembali menjadi tampilan awal "Welcome to nginx!".

### 5. Bandingkan waktu dan langkah memasang Nginx di pertemuan 2 (dengan apt) dan di pertemuan ini (dengan Docker). Apa keuntungan container bagi tim yang harus menjalankan aplikasi yang sama di banyak server cloud?

Pada pertemuan 2, pemasangan Nginx dilakukan secara manual menggunakan perintah `apt update` dan `apt install`. Proses tersebut membutuhkan beberapa langkah untuk memasang dan mengatur aplikasi pada sistem operasi.

Sedangkan pada praktikum ini, Nginx dijalankan menggunakan Docker dengan perintah `docker run`. Aplikasi dapat langsung dijalankan dari image yang sudah tersedia tanpa harus memasang Nginx secara manual.

Keuntungan menggunakan container adalah proses menjalankan aplikasi menjadi lebih cepat, mudah, dan konsisten. Tim juga dapat menggunakan image yang sama pada banyak server cloud sehingga konfigurasi aplikasi lebih seragam dan mengurangi kemungkinan kesalahan saat instalasi.

## 9. Kesimpulan

Berdasarkan praktikum yang telah dilakukan, Docker merupakan platform yang memudahkan pengguna dalam menjalankan aplikasi menggunakan container. Melalui praktikum ini, mahasiswa mempelajari cara mengunduh image, membuat container, menjalankan web server Nginx, melakukan pemetaan port, memeriksa log, serta mengelola siklus hidup container.

Praktikum ini juga menunjukkan bahwa container memiliki kelebihan dibandingkan Virtual Machine dalam hal penggunaan kernel host dan ukuran yang lebih ringan. Selain itu, penggunaan Docker mempermudah proses menjalankan aplikasi secara konsisten pada berbagai lingkungan server.

Dengan demikian, praktikum ini memberikan pemahaman dasar mengenai penggunaan Docker sebagai salah satu teknologi dalam Cloud Computing.
