# howtosisak
Repo ini berisi dokumentasi cara login ke [SISAK Polsri](https://sisak1.polsri.ac.id/mahasiswa/) menggunakan `curl`.

## Langkah-langkah

### 1. Ambil halaman login
Kita perlu ambil cookie awal + CSRF token.

curl -s -k -c cookie.txt -o login.html \
-H "User-Agent: Mozilla/5.0" \
https://sisak1.polsri.ac.id/mahasiswa/

#### Penjelasan Command
`-s` silent mode, untuk lebih sedikit output  
`-k` skip verifikasi tls  
`-c` cookie jar / menyimpan semua cookie halaman ke cookie.txt  
`-H` untuk header  

### 2. Ambil Token CSRF  

`TOKEN=$(grep -oP 'name="csrf_has_name" value="\K[^"]+' login.html)`  
#### Penjelasan Command  
`TOKEN=..` ikin variabel di shell bernama TOKEN  
`$(. . .)` ommand Substitution -> jalankan perintah didalamnya dan ambil outputnya  
`grep ..`  cari teks  
`-o`       hanya tampilkan bagian yang cocok bukan seluruh baris  
`-P`       pakai PCRE (REGEX YANG LEBIH KUAT)    
##### Pola REGEX:  
`name="csrf_hass_name value="` cari atribut itu  
`\K` Reset / awal hasil tangkap ( jadi yang sebelum \K tidak ikut hasil)  
`[^"]` ambil semua karakter sampai tanda kutip berikutnya (nilai token)   

#### Hasilnya variabel token akan berisi CSRF  

### 3. Kirim Login Form Dengan Cookie + Token  
curl -s -k -L -i -b cookie.txt -c cookie.txt \ 
-H "User-Agent: Mozilla/5.0" \  
-H "Content-Type: application/x-www-form-urlencoded" \  
-H "Referer: https://sisak1.polsri.ac.id/mahasiswa/" \   
-H "Origin: https://sisak1.polsri.ac.id" \   
--data "username=[Usernamemu]&password=[Passwordmu]&csrf_has_name=$TOKEN" \   
https://sisak1.polsri.ac.id/mahasiswa/site/login   

#### Penjelasan Command  
`-L` follow redirect, jadi setelah login langsung redirect ke halaman /beranda  
`-i` untuk menampilkan respon http  
`-b` untuk membaca dan menuliskan ulang dengan cookie yang baru stelah login `-c`  
`-H` untuk header, beberapa web perlu header ini untuk login  
`--data` digunakan untuk mengirimkan data kita, ganti [usernamemu] dan [passwordmu] dengan login sisakmu yang benar  
`https://sisak1.polsri.ac.id/mahasiswa/site/login` endpoint yang digunakan untuk login sisak mahasiswa polsri  

## CIAO!







