---
title: "HTB Writeup - Resolute"
date: 2020-06-02T11:30:31+07:00
draft: false
tags: [ "Windows"]
categories: [ "HTB"]
---

![](/images/htb/resolute/Resolute.png)

## Overview
Mesin ini dibuat oleh [egre55](https://www.hackthebox.eu/home/users/profile/1190) , sistem operasi yang digunakan adalah Windows dengan level kesulitan **Medium** dan memiliki point sebesar 30 point. Mesin ini direlease pada tanggal 07 Desember 2019 dan retired pada tanggal 30 Mei 2020.


## Scanning & Enumeration

### Port scanning

Pertama kita akan memulainya dengan melakukan scanning terhadap target menggunakan nmap, dari hasil scanning kita mendapatkan informasi berupa port ataupun service yang berjalan dan dapat diakses dari external network. 

    nmap -sV -sC -oA nmap/resolute 10.10.10.169 -vv

![](/images/htb/resolute/1.png)

### Port SMB (445 & 139)

Nmap memberikan informasi bahwa port 445 dan 139 yang digunakan service SMB terbuka, sehingga kita dapat melakukan enumerasi lebih jauh terhadap service ini. Ada beberapa tools yang dapat digunakan untuk enumerasi service SMB, diantaranya adalah:

    ⁃   Nmap
    ⁃   Nmblookup
    ⁃   Nbtscan
    ⁃   SMBMap
    ⁃   Smbclient
    ⁃   Rpcclient
    ⁃   Enum4linux

dari hasil scanning nmap sebelumnya, kita mendapati informasi sebagai berikut :

![](/images/htb/resolute/2.png)

### Users Enumeration

Setelah menggunakan beberapa tools diatas, kita dapat login sebagai anonymous user namun tidak mendapatkan akses yang berarti.  Kemudian kita coba menggunakan tool rpcclient (default port 139) dan berhasil mendapatkan informasi users yang ada pada mesin target, untuk mendapatkan informasi yang lebih cepat kita bisa menggunakan tool enum4linux, berikut hasil dari rpcclient: 

![](/images/htb/resolute/3.png)

Menggunakan rpcclient kita dapat melakukan query secara manual terhadap setiap user yang ada, jika kita mengejar waktu maka menggunakan tools ini tidak disarankan. Kemudian didapatkan infromasi yang menarik pada user marko dengan rid 0x457 :

![](/images/htb/resolute/4.png)

### Bruteforce Users

Kita memiliki informasi bahwa user marko diset dengan password Welcome123!, kemudian dilakukan percobaan login dengan credential marko:Welcome123! namun tidak mendapatkan hasil yang kita harapkan. Dengan memanfaatkan userlist yang kita dapatkan dari rpcclient, kita melakukan percobaan bruteforce dengan password Welcome123! terhadap userlist yang kita miliki, dan didapati credential yang valid  yaitu **melaine:Welcome123!**


![](/images/htb/resolute/5.png)

## Gaining Access

## Initial Shell & Get User.txt

Setelah mendapatkan valid credential, kita berusaha mendapatkan akses shell dengan memanfaatkan port 5985 menggunakan tools Evil-WinRM

![](/images/htb/resolute/6.png)

User Flag : 0c3be45fcfe249796ccbee8d3a978540

## Privilege Escalation

Setelah melakukan enumerasi yg cukup melelahkan pada sistem target, didapatkan hidden folder dan file pada sistem target yang memuat informasi credential user yaitu **ryan:Serv3r4Admin4cc123!**

![](/images/htb/resolute/7.png)

Setelah mendapatkan credential dari user ryan, kemudian dilakukan pengecekan privilege yang dimiliki oleh user ryan, dan diapati bahwa user ryan tidak memiliki privilege yang menarik, namun user ryan adalah member dari group DnsAdmins.

![](/images/htb/resolute/8.png)

Jika user adalah membership dari group DNSAdmins, maka kita dapat abuse membership ini untuk privilege escalation menjadi Administrator. Dengan memanfaatkan layanan DNS pada target, kita akan mencoba menginjeksi DLL lalu mendapatkan code execution dengan privilege SYSTEM.


### Building DLL 

Seperti yang kita sebut sebelumnya, kita akan menginjeksi proses dns.exe dengan malicious DLL yang kita siapkan, untuk membuktikan cara ini berkerja atau tidak kita buat DLL untuk melakukan reverse shell ke mesin kita.

![](/images/htb/resolute/9.png)

### Abuse DNS with dnscmd

Sekarang kita memiliki DLL untuk reverse shell, lalu kita setting agar target (Resolute.megabank.local) melakukan pointing ke DLL milik kita menggunakan dnscmd, make sure bahwa target benar-benar sudah pointing ke DLL milik kita. 

![](/images/htb/resolute/10.png)

![](/images/htb/resolute/11.png)

kenapa DLL kita dipointing secara remote smb ke mesin kita? kita memilih ini, karena windows secara default mendukung path UNC dan samba share dalam kebanyakan kasus.


### Getting Code Execution With NT\SYSTEM

Sekarang waktunya melakukan restart DNS Service, maka DLL milik kita secara otomatis akan di-load oleh  proses dns.exe dan reverse shell akan dikirimkan ke listener kita.

![](/images/htb/resolute/12.png)


### Flag Root.txt
Setelah tahapan restart service sudah dilakukan, tunggu beberapa saat dan kitapun akan mendapatkan akses shell dengan user sebagai NT AUTHORITY\SYSTEM

![](/images/htb/resolute/13.png)

Root Flag : e1d94876a506850d0c20edb5405e619c

Terimakasih, Kalo di rasa tulisan ini bermanfaat, silahkan Share. Semoga kebermanfaatan ini terus berlanjut!








