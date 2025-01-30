| Name           | NRP        | Kelas     |
| ---            | ---        | ----------|
| Yasmin Putri Sujono | 5025221273 | Jaringan Komputer A |


## Put your topology config image here!
<img width="805" alt="Screenshot 2024-12-06 at 05 02 52" src="https://github.com/user-attachments/assets/25ee27d2-2c06-4ef6-a547-96010c50ed20">

## Put your VLSM calculation here!
<img width="576" alt="Screenshot 2024-12-06 at 20 56 31" src="https://github.com/user-attachments/assets/1f7f7e7e-44e0-4842-87be-e18934f0fb1f">

<b>Tree:<br>
<img width="952" alt="Screenshot 2024-12-06 at 21 46 51" src="https://github.com/user-attachments/assets/ad821a75-9cd7-457f-ac2a-bfcbeca590ec">

## Put your IP route here!
<img width="662" alt="Screenshot 2024-12-06 at 20 53 28" src="https://github.com/user-attachments/assets/35ee0311-a274-4dd7-a5ae-aedc550d4a0f">

## Soal 1

> Bagaimana cara mengkonfigurasi iptables pada router bernama 'Heiji' agar memungkinkan jaringan mengakses internet melalui interface eth0 menggunakan SNAT tanpa menggunakan MASQUERADE?

> _How to configure iptables on a router named ‘Heiji’ to allow the network to access the internet through interface eth0 using SNAT without using MASQUERADE?_

**Answer:**

- Screenshot <br>
Testing Agasa (Setelahnya) <br>
<img width="776" alt="Screenshot 2024-12-07 at 15 24 55" src="https://github.com/user-attachments/assets/cae36a9f-d6e5-45ed-ba88-3f62899ac965"> <br>

- Configuration & Explanation <br>
Menggunakan bash scripting untuk secara otomatis mendapatkan alamat IP dari interface eth0 dan mengatur aturan SNAT pada iptables.

```
IPETH0="$(ip -br a | grep eth0 | awk '{print $NF}' | cut -d'/' -f1)"
```
ip -br a: Menampilkan semua interface jaringan dengan format ringkas. <br>
grep eth0: Menyaring informasi hanya untuk interface eth0. <br>
awk '{print $NF}': Mengambil kolom terakhir (yang berisi alamat IP dengan subnet mask <br>
cut -d'/' -f1: Memisahkan alamat IP dari subnet mask, sehingga hanya menghasilkan IP <br>

```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source "$IPETH0" -s 10.4.0.0/20
```
-t nat: Menargetkan tabel NAT untuk pengubahan alamat. <br>
-A POSTROUTING: Menambahkan aturan ke chain POSTROUTING (dijalankan setelah routing). <br>
-o eth0: Aturan berlaku untuk paket yang keluar melalui interface eth0.<br>
-j SNAT: Menentukan target SNAT untuk mengubah alamat sumber paket.<br>
--to-source "$IPETH0": Mengatur alamat sumber menjadi alamat IP dari eth0 yang telah disimpan dalam variabel $IPETH0. <br>
-s 10.4.0.0/20: Membatasi aturan hanya untuk lalu lintas dari subnet 10.4.0.0/20. <br>


<br>

## Soal 2

> Kalian diminta untuk melakukan drop semua paket masuk TCP kecuali pada port 1744 pada node Shinichi.

> _You are asked to drop all incoming TCP packets except on port 1744 on Shinichi's node_

**Answer:**

- Screenshot
    - Benar <br>
<img width="626" alt="Screenshot 2024-12-06 at 17 52 46" src="https://github.com/user-attachments/assets/8c167226-bd74-4637-bd86-046d0f35b05c"> <br>

- Configuration & Explanation <br> 
Untuk melakukan drop semua paket masuk TCP kecuali pada port 1744 di node Shinichi, berikut adalah penjelasan langkah-langkahnya

```
apt update
apt install netcat -y
apt install lynx -y
```
 Instal Netcat untuk Pengujian Koneksi dan  Instal Lynx untuk Pengujian HTTP
 <br>
 
`iptables -A INPUT -p tcp --dport 1744 -j ACCEPT` <br> Membuat aturan di tabel INPUT untuk menerima semua paket masuk dengan protokol TCP yang menuju ke port 1744. <br>
<br>
`iptables -A INPUT -p tcp ! --dport 1744 -j DROP` <br> Membuat aturan di tabel INPUT untuk menolak semua paket TCP yang tidak menuju ke port 1744. <br>

- Cara Kerja Aturan Iptables <br>
Paket TCP masuk:
    - Jika tujuannya adalah port 1744, akan diterima (ACCEPT). Jika tujuannya selain port 1744, akan dibuang (DROP).
    - Aturan berjalan berurutan, sehingga aturan pertama (menerima pada port 1744) diproses lebih dahulu sebelum aturan kedua (menolak semua selain port 1744).

- Uji Aturan <br>
    - nc -zv <IP_NODE_SHINICHI> 1744 : Gunakan Netcat untuk memastikan koneksi ke port 1744 diterima <br>
    - nc -zv <IP_NODE_SHINICHI> 80 : Hasilnya, koneksi ke port selain 1744 harus ditolak.
- Testing <br> 
CONAN : nc -l -p 1744 (benar) <br> 
TESTING : nc 10.4.0.131 1744 (benar) <br>

<br>

## Soal 3

> Lakukan pembatasan sehingga koneksi SSH pada semua Web Server hanya dapat dilakukan oleh user yang berada pada node Ran.

> _Make restrictions so that SSH connections to all Web Servers can only be made by users who are on the Ran node._

**Answer:**

- Screenshot <br> 
<img width="635" alt="Screenshot 2024-12-07 at 00 37 57" src="https://github.com/user-attachments/assets/8d2c419b-1b2e-4c8f-a11e-616d29081627"> <br>
<br>
TESTING <br>
<img width="587" alt="Screenshot 2024-12-07 at 00 38 10" src="https://github.com/user-attachments/assets/d8899dc7-1980-49c6-a3b7-1a4d8c4290b8"> <br>
<img width="641" alt="Screenshot 2024-12-07 at 00 38 21" src="https://github.com/user-attachments/assets/cbb7e0d9-6e2e-459f-bd8d-93d065b27f3c"> <br>

- Configuration & Explanation <br>
CONAN
  ```
  iptables -A INPUT -p tcp -s 10.4.4.2 --dport 22 -j ACCEPT
  ```
-A INPUT: Menambahkan aturan untuk paket masuk. <br>
-p tcp: Membatasi aturan untuk protokol TCP. <br>
-s 10.4.4.2: Membatasi aturan hanya untuk koneksi yang berasal dari IP node Ran  <br>
--dport 22: Membatasi aturan hanya pada koneksi menuju port 22 (default port SSH). <br>
-j ACCEPT: Paket yang sesuai aturan ini akan diizinkan (ACCEPT). <br>
<br>

    `iptables -A INPUT -p tcp --dport 22 -j DROP`
-p tcp: Membatasi aturan untuk protokol TCP. <br>
--dport 22: Membatasi aturan hanya pada koneksi menuju port 22. <br>
-j DROP: Paket yang sesuai aturan ini akan diblokir. <br>

- Cara Kerja Aturan <br>
Urutan aturan sangat penting karena iptables memproses aturan secara sekuensial:
    - Aturan pertama mengizinkan koneksi dari node Ran ke port 22.
    - Aturan kedua memblokir koneksi lain ke port 22.

- Uji Koneksi<br>
    - Tes Koneksi dari Node Ran
Gunakan perintah SSH dari node Ran:
`nc 10.4.0.130 22`
Koneksi harus berhasil.

    - Tes Koneksi selain Node Ran
Gunakan perintah SSH dari node selain Ran:
`nc 10.4.0.130 22`
Hasilnya: Koneksi harus gagal.


<br>

## Soal 4

> Semua subnet hanya dapat mengakses Web Server pada port 80 dan 443 (Conan, Sonoko, dan Megure) pada hari Senin-Jumat, pukul 07:00- 19:00.

> _All subnets can access the Web Server (Conan, Sonoko, and Megure) only on ports 80 and 443, from Monday to Friday, between 07:00-19:00._

**Answer:**

- Screenshot<br> 
    - benar <br>
<img width="651" alt="Screenshot 2024-12-07 at 15 47 24" src="https://github.com/user-attachments/assets/286a711d-92cc-458a-a5eb-585f575fc5d6"> <br> 
    - salah <br>
<img width="666" alt="Screenshot 2024-12-07 at 15 47 41" src="https://github.com/user-attachments/assets/d998db86-e53c-48c2-9379-6363fec3d430">

- Configuration & Explanation
Untuk mengatur pembatasan agar semua subnet hanya dapat mengakses Web Server pada port 80 dan 443 (Conan, Sonoko, dan Megure) pada hari Senin hingga Jumat pukul 07:00-19:00 menggunakan
```
iptables -A INPUT -m time --timestart 07:00 --timestop 19:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
```
-A INPUT : Menambahkan aturan (append) ke chain INPUT, yang menangani lalu lintas masuk. <br>
-m time : Menggunakan modul waktu (time module) untuk membuat aturan berdasarkan waktu atau hari tertentu.<br>
--timestart 07:00 : Aturan mulai berlaku pada pukul 07:00 (format 24 jam).<br>
--timestop 19:00 : Aturan berhenti berlaku pada pukul 19:00 (format 24 jam).<br>
--weekdays Mon,Tue,Wed,Thu,Fri : Aturan hanya berlaku pada hari kerja: Senin hingga Jumat.<br>
-j ACCEPT : Jika kriteria terpenuhi, lalu lintas akan diterima (ACCEPT).<br>

`iptables -A INPUT -j REJECT` untuk menolak semua paket yang tidak cocok dengan aturan sebelumnya, dan mengirimkan pesan penolakan ke pengirim.

- Urutan Aturan <br>
Urutan aturan sangat penting dalam iptables:
    - Aturan pertama dan kedua mengizinkan akses ke port 80 dan 443 selama waktu yang ditentukan (07:00-19:00, Senin-Jumat).
    - Aturan ketiga menolak semua koneksi lain.

- Uji Aturan
  Berhasil
    - Di set terlebih dahulu `date --set="2024-12-3 15:00:00"`
    - `nc -l -p 80` pada Conan
    - `nc 10.4.0.130 80` pada Ran
  Gagal
    - `date --set="2024-12-3 20:00:00"` Pada Conan
    - `nc -l -p 80` pada Conan
    - Melakukan uji coba `ping 10.4.0.130` dan nc `10.4.0.130 80`pada Ran
 
<br>

## Soal 5

> Ternyata subnet Haibara memiliki akses tambahan, yaitu dapat mengakses Web Server pada port 80 dan 443 di luar hari Senin-Jumat (hari Sabtu dan Minggu), tanpa pembatasan waktu.

> _The Haibara subnet has additional access, allowing it to access the Web Server on ports 80 and 443 outside of Monday to Friday (on Saturday and Sunday), with no time restrictions._

**Answer:**

- Screenshot

    ![image](https://github.com/user-attachments/assets/343c5249-7ac8-41cb-bdd7-c24b82f00968)

- Configuration & Explanation
Untuk mengatur aturan agar subnet Haibara memiliki akses tambahan ke Web Server pada port 80 dan 443 pada hari Sabtu dan Minggu tanpa pembatasan waktu, sedangkan subnet lainnya tetap memiliki akses hanya pada hari Senin-Jumat dari pukul 07:00 hingga 19:00. <br>
<br>
`iptables -A INPUT -s 10.4.8.2/21 -p tcp -m multiport --dports 80,443 -m time --weekdays Sat,Sun -j ACCEPT`
<br>
-s 10.4.8.2/21: Membatasi aturan untuk subnet Haibara. <br>
-p tcp: Membatasi aturan untuk protokol TCP. <br>
-m multiport --dports 80,443: Mengizinkan akses pada port 80 (HTTP) dan 443 (HTTPS). <br>
-m time --weekdays Sat,Sun: Aturan ini berlaku hanya pada hari Sabtu dan Minggu. <br>
-j ACCEPT: Paket yang sesuai aturan ini akan diterima. <br>
<br>
TESTING CONAN <br> 
`date --set="2024-12-1 20:00:00"` dan `nc -l -p 80`<br>
<br>
-  Uji Aturan <br>
    - HAIBARA (berhasil) : nc 10.4.0.130 80 <br>
    - RAN (gagal) : nc 10.4.0.130 80 <br>


<br>

## Soal 6

> Akses ke Web Server Conan, Sonoko, dan Megure pada port 80 dan 443 dilarang pada hari Jumat, pukul 11:00-13:00 (maklum, Jumatan rek).

> _Access to the Web Server (Conan, Sonoko, and Megure) on ports 80 and 443 is prohibited on Fridays between 11:00-13:00 (It's Friday prayer time)._

**Answer:**

- Screenshot

    ![image](https://github.com/user-attachments/assets/ec8ae7cd-bbc3-4510-9d38-8e6b231a080a)

- Configuration

    ```
    iptables -I INPUT -p tcp --dport 80 -m time --weekdays Fri --timestart 11:00 --timestop 13:00 -j DROP
    iptables -I INPUT -p tcp --dport 443 -m time --weekdays Fri --timestart 11:00 --timestop 13:00 -j DROP
    ```

- Explanation

    Membuat peraturan akses ke web dimana akses dilarang saat waktu ibadah sholat jumat


<br>

## Soal 7

> Tambahkan logging paket yang di-drop di setiap node server dan router.

> _Enable logging for dropped packets on every server node and router._

**Answer:**

- Screenshot

   ![image](https://github.com/user-attachments/assets/003bee1b-28d3-4fdf-af40-744df5e6cb07)

- Configuration

  ```
    Webserver
    iptables -A INPUT  -j LOG --log-level debug --log-prefix 'Package Dropped' -m limit --limit 1/second --limit-burst 10

    Router
    iptables -A FORWARD  -j LOG --log-level debug --log-prefix 'Package Dropped' -m limit --limit 1/second --limit-burst 10
  ```




<br>

## Soal 8

> Untuk keperluan maintenance, pilih salah satu Subnet dan lakukan blokir terhadap semua request protokol ICMP (ping) dari luar subnet terhadap subnet tersebut.

> _For maintenance purposes, select one Subnet and block all ICMP (ping) protocol requests from outside the subnet to that subnet._

**Answer:**

- Screenshot

    ![image](https://github.com/user-attachments/assets/ead79481-c1ea-48da-9ca8-837559bb5769)


- Configuration

    ```
    iptables -A INPUT -p icmp -s ! 10.4.2.0/23 -d 10.4.2.0/23 -j DROP
    iptables -A INPUT -p icmp -s 10.4.2.0/23 -d 10.4.2.0/23 -j ACCEPT
    ```

- Explanation

    Pada subnet dengan NID 10.4.2.0 tidak bisa melakukan ping diluar subnet, akan tetapi yang didalam subnet masih bisa melakukan ping

<br>

## Soal 9

> Untuk meningkatkan keamanan, setiap Web Server harus bisa melakukan blok terhadap IP yang melakukan scanning port dalam jumlah yang tidak wajar (maksimal 10 scan port) di dalam selang waktu 1 menit. 

> _To enhance security, each Web Server must be able to block IP addresses that perform an excessive number of port scans (a maximum of 10 port scans) within a 1-minute interval._

**Answer:**

- Screenshot

    ![image](https://github.com/user-attachments/assets/27ca1044-15f5-4e9b-b063-30dc5d1f90b4)

- Configuration

    ```
    iptables -N portscan
    iptables -A INPUT -m recent --name portscan --update --seconds 60 --hitcount 10 -j DROP
    iptables -A FORWARD -m recent --name portscan --update --seconds 60 --hitcount 10 -j DROP
    iptables -A INPUT -m recent --name portscan --set -j ACCEPT
    iptables -A FORWARD -m recent --name portscan --set -j ACCEPT

    ```

- Explanation

    Konfigurasi iptables ini mendeteksi dan memblokir aktivitas port scanning dengan memantau jumlah koneksi dari sebuah IP dalam 60 detik. Jika sebuah IP mengirim lebih dari 10 koneksi dalam waktu tersebut, maka koneksi selanjutnya akan diblokir. Aturan ini berlaku untuk paket masuk (INPUT) dan diteruskan (FORWARD), memastikan server terlindungi dari aktivitas scanning berlebihan.

<br>

## Soal 10

> Akses dari client ke WebServer Conan pada port 80 akan didistribusikan bergantian antara Web Server Conan dan Web Server Sonoko. Sebaliknya akses pada Web Server Sonoko pada port 443 akan didistribusikan bergantian antara Web Server Conan dan Web Server Sonoko. Lakukan evaluasi menggunakan Jmeter terhadap berbagai metode load balancing seperti Round-Robin dan lain-lainnya.

> _Client access to the Conan Web Server on port 80 should be alternated between the Conan Web Server and the Sonoko Web Server. Conversely, access to the Sonoko Web Server on port 443 should be alternated between the Conan Web Server and the Sonoko Web Server. (Evaluate various load balancing methods, such as Round-Robin and others, using JMeter.)_

**Answer:**

- Screenshot

    ![image](https://github.com/user-attachments/assets/26124e37-8640-4489-a7dd-4f0e77564e47)


- Configuration

    ```
    #  Web Server Conan
  echo "nameserver 192.168.122.1" > /etc/resolv.conf

  apt update
  apt install nginx

  echo "server {
  listen 80;

      server_name 10.4.0.130;

      location / {
          root /var/www/html;
          index index.html index.htm;
      }

  }
  " > /etc/nginx/sites-available/default

  echo "Conan Si anime" > /var/www/html/index.html

  service nginx restart

    #   Web server sa
    echo "nameserver 192.168.122.1" > /etc/resolv.conf
  apt update
  apt install nginx

  echo "server {
  listen 80;

      server_name 10.4.0.18;

      location / {
          root /var/www/html;
          index index.html index.htm;
      }

  }
  " > /etc/nginx/sites-available/default

  echo "Sadako Lucu" > /var/www/html/index.html

  service nginx restart

    # Load Balancer Kogoro

  echo "nameserver 192.168.122.1" > /etc/resolv.conf
  apt update
  apt install nginx

  echo "upstream webserver {
  server 10.4.0.130:80;
  server 10.4.0.18:80;
  }

  server {
  listen 80;

      location / {
          proxy_pass http://webserver;
          proxy_set_header Host \$host;
          proxy_set_header X-Real-IP \$remote_addr;
          proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto \$scheme;
      }

  }
  " > /etc/nginx/sites-available/default

  service nginx restart




    # Kazuha router  
    iptables -A PREROUTING -t nat -p tcp -d 10.4.0.130 --dport 80 -m statistic --mode nth --every 2 --packet 1 -j DNAT --to-destination 10.4.0.130:80
    iptables -A PREROUTING -t nat -p tcp -d 10.4.0.130 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.4.0.18:80
    # testing
    ab -n 500 -c 100 -l http://10.4.0.130/
    ```

- Explanation

    Konfigurasi menggunakan Nginx sebagai load balancer di Kogoro dengan metode Round-Robin, mengarahkan lalu lintas HTTP antara Web Server Conan dan Sonoko pada port 80. Router Kazuha menggunakan iptables untuk mendistribusikan paket secara bergantian ke kedua server. Pengujian dilakukan dengan Apache Benchmark (ab), memastikan load balancing bekerja sesuai harapan dengan pemerataan distribusi permintaan


<br>
