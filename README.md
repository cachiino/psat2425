# Penilaian Sumatif Akhir Tahun
## Mapil DevOps XI TJKT 1 - Penilaian Praktek
### SMKN 1 Banyumas - TA. 2024 2025


# Cara mendeploy Aplikasi Menggunakan File .env dan Otomatis.sh
Sebelum deploy aplikasi kita harus paham dulu apa itu file .env dan apasih gunanya?
Nah buat temen-temen yang belum tau, file .env adalah file environment sistem yang gunanya itu buat menyimpan pengaturan rahasia atau data penting yang tidak boleh ditulis langsung dalam program. File .env ini mirip mirip sama file config.php loh!
Contoh data yang bisa dimasukan ke file .env adalah :
- Nama database
- Username database
- Password
- Host (alamat server)

Contoh isi file .env :
```
.env
DB_USER=admin
DB_PASS=P4ssw0rd123
DB_NAME=psat2425
DB_HOST=rdsku.czt6n8ylfvyb.us-east-1.rds.amazonaws.com
```

Sekarang temen-temen udah paham kan apa itu file .env, setelah ini ada cara deploy aplikasi secara otomatis menggunakan shell script jugaa!!

## PERSIAPAN
### 1. Buat repository baru di github
### 2. Masuk ke folder yang berisi kode program menggunakan VScode atau text editor lainnya.
### 3. Tambahkan file ".env" dan biarkan isinya kosong
### 4. Pastikan sudah ada atau buat file ".gitignore"
File .gitignore adalah file yang memberitahu git untuk mengabaikan file atau folder tertentu saat akan push ke github ataupun commit ke repo lokal. File .gitigone ini penting banget buat sembunyiin file .env kita supaya tetap aman dan rahasia.
Contoh isi file .gitignore :
```
.env                  #> Abaikan file .env (isi data sensitif)
uploads/*             #> Abaikan semua isi folder uploads
!uploads/             #> Tapi jangan abaikan folder uploads itu sendiri
```

### 5. Push program ke github 
<pre>Cara push : 
a. buka terminal 
    Klik Terminal > New Terminal 
    atau bisa juga 
    Tekan Ctrl=Shift+'
b. Ketik > git init
c. git remote add origin [tambahkan link repository]
    contoh : git remote add origin https://github.com/cachiino/psat2425
d. git add .
e. git commit -m "first commit"
f. git branch -M main
g. git push -u origin main
</pre>
### 6. Buka AWS dan buat database menggunakan "Aurora and RDS"
a. Buka "Aurora and RDS"
b. Klik "Databases"
c. Tekan "Create database"
d. Engine options pilih "MySQL"
e. Templates "Free tier"
f. Beri nama pada bagian "DB instance identifier"
g. Beri username pada "Master username"
h. Buat password pada "Master password"
i. Biarkan Public access tetap No
j. Tambahkan security groups yang mengizinkan port 3306 (MySQL / MariaDB)
k. Jika sudah klik "Create database"
l. Tunggu hingga "Endpoint" muncul

## CARA 1
### 1. Buat Instances
a. Beri nama instance pada bagian "Name"
b. Pilih OS Ubuntu
c. Instance type : t2.micro
d. Key pair : vockey
e. Security group : 
Allow SSH (port 22)
Allow HTTPS (port 443)
Allow HTTP (port 80)
Anywhere 0.0.0.0/0

### 2. Tambahkan shell script
a. Masih di halaman "launch an instance"
b. Klik "Advanced details"
c. Scroll sampai pada bagian "User data"
d. Tambahkan :
```
#!/bin/bash
echo '#!/bin/bash
sudo apt update -y
sudo apt install -y apache2 php php-mysql libapache2-mod-php mysql-client
sudo rm -rf /var/www/html/{,.}
sudo git clone [repository githubmu] /var/www/html
sudo chmod -R 777 /var/www/html
echo DB_USER=[username rds] > /var/www/html/.env
echo DB_PASS=[password rds]  >> /var/www/html/.env
echo DB_NAME=[nama database]  >> /var/www/html/.env
echo DB_HOST=[endpoint rds] >> /var/www/html/.env
sudo apt install -y openssl
sudo a2enmod ssl
sudo a2ensite default-ssl.conf
sudo systemctl reload apache2' > /home/ubuntu/otomatis.sh

chmod +x /home/ubuntu/otomatis.sh  
```
Penjelasan :
- contoh sudo git clone > sudo git clone https://github.com/cachiino/psat2425 /var/www/html/.env
- DB_USER berisi username yang tadi dibuat di RDS (Master Username)
- DB_PASS berisi password yang tadi dibuat di RDS (Master Password)
- DB_NAME berisi nama database ubtuk aplikasi kamu (boleh buat sendiri)
- DB_HOST berisi endpoint di database "Aurora and RDS"

e. Pastikan semuanya sudah terisi dengan benar, jika sudah klik "Launch Instance"
f. Jika instance tidak muncul refresh halaman instances 

### 3. Buka terminal instance
a. klik "Instance ID" ke instance yang sudah kita buat tadi
b. Klik "Connect"
c. Scroll sedikit ke bawah dan klik "Connect"

### 4. Jalankan shell script yang sudah tadi dibuat
Gunakan perintah :
> $ ./otomatis.sh  

### 5. Cek akses menggunakan ip


## Cara 2 
### 1. Buka terminal instance
a. klik "Instance ID" ke instance yang sudah kita buat tadi
b. Klik "Connect"
c. Scroll sedikit ke bawah dan klik "Connect"

#2. Tuliskan perintah untuk menjalankan script :
a. $ nano otomatis.sh
tuliskan shell script sama seperti cara 1
``` 
#!/bin/bash
echo '#!/bin/bash
sudo apt update -y
sudo apt install -y apache2 php php-mysql libapache2-mod-php mysql-client
sudo rm -rf /var/www/html/{,.}
sudo git clone [repository githubmu] /var/www/html
sudo chmod -R 777 /var/www/html
echo DB_USER=[username rds] > /var/www/html/.env
echo DB_PASS=[password rds]  >> /var/www/html/.env
echo DB_NAME=[nama database]  >> /var/www/html/.env
echo DB_HOST=[endpoint rds] >> /var/www/html/.env
sudo apt install -y openssl
sudo a2enmod ssl
sudo a2ensite default-ssl.conf
sudo systemctl reload apache2' > /home/ubuntu/otomatis.sh

chmod +x /home/ubuntu/otomatis.sh  
```
b. Ketik $ chmod +x otomatis.sh
c. Jalankan dengan $ ./otomatis.sh

### 3. Coba akses menggunakan ip

Jika masuk, Selamat! kalian berhasil deploy aplikasi otomatis menggunakan shell scriptt🥳‼️

Penjelasan tambahan mengenai shell script dan fungsi dari masing masing perintah : 
Shell script adalah file teks yang berisi kumpulan perintah (commands) yang dijalankan secara otomatis oleh shell. Jadi, kamu ga perlu ngetik perintah satu-satu di terminal, bisa kamu tulis semuanya langsung dalam satu file .sh, lalu jalankan, praktis banget kan! 

## Fungsi masing-masing perintah yang ada di otomatis.sh tadi 
1. sudo apt update -y > update daftar paket terbaru dari internet
2. sudo apt install -y apache2 php php-mysql libapache2-mod-php mysql-client > untuk menginstall apache2, php, php-mysql dll
3. sudo rm -rf /var/www/html/{,.} > menghapus semua isi folder /var/www/html termasuk file tersembunyi
4. sudo git clone [repository githubmu] /var/www/html > clone proyek dar github ke web server (/var/www/html)
5. sudo chmod -R 777 /var/www/html > memberikan izin ke semua orang agar bisa read/write/execute
6. sudo apt install -y openssl > untuk menginstall openssl
7. sudo a2enmod ssl > aktifkan modul ssl di apache agar bisa HTTPS
8. sudo a2ensite default-ssl.conf > aktifkan konfigurasi situs ssl default
9. sudo systemctl reload apache2 > restart/reload apache untuk menerapkan perubahan
10. chmod +x /home/ubuntu/otomatis.sh > memberi izin eksekusi agar file script bisa dijalankan

## Uji coba menjalankan
Jalakan menggunakan username dan password default berikut ini :
# Username = admin
# Password = 123

## ✨‼️SELAMAT MENCOBA DAN SEMOGA BERHASIL‼️✨‼ 