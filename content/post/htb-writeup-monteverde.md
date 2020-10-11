---
title: "HTB Writeup - Monteverde"
date: 2020-06-13T13:31:26+07:00
draft: false
tags: [ "Windows"]
categories: [ "HTB"]
---

![](/images/htb/montervede/montervede.png)

## Overview
Mesin ini dibuat oleh [egre55](https://www.hackthebox.eu/home/users/profile/1190) , sistem operasi yang digunakan adalah Windows dengan level kesulitan Medium dan memiliki point sebesar 30 point. Mesin ini direlease pada tanggal 11 Januari 2020 dan retired pada tanggal 13 Juni 2020.


## Scanning & Enumeration

### Port scanning

Pertama kita akan memulainya dengan melakukan scanning terhadap target menggunakan nmap, dari hasil scanning kita mendapatkan informasi berupa port ataupun service yang berjalan dan dapat diakses dari external network. 

    nmap -sV -sC -oA nmap/monteverde 10.10.10.172 -vv -Pn

![](/images/htb/montervede/1.png)

### Users Enumeration
Dari informasi yang diberikan oleh nmap, kita dapat memanfaatkan service SMB untuk melakukan enumerasi users yang ada pada sistem target menggunakan tools seperti rpcclient :

![](/images/htb/montervede/2.png)

dengan menggunakan rpcclient kita mendapatkan userlist dari sistem target, yg kemudian kita simpan userlist tersebut untuk kebutuhan bruteforce service SMB. Jangan berfikir untuk langsung menggunakan wordlist lain (ex rockyou.txt) jika kita belum mencoba dari apa yang kita miliki sekarang, terkadang seorang admin/user malas untuk menggunakan complex password sehingga bisa jadi pola credentialnya seperti admin:admin. 

### Bruteforce Users

Kita akan melakukan bruteforce terhadap service SMB, disini kita menggunakan tools Hydra , namun tidak mendapatkan hasil yang kita harapkan. Lalu? coba menggunakan wordlist rockyou.txt? nope! 

![](/images/htb/montervede/3.png)

jika kita melihat lagi ke informasi yang diberikan oleh Nmap , sistem target memiliki service LDAP dengan port 389 yang terbuka, maka kita coba melakukan bruteforce ke service ini dan berhasil mendapatkan valid credential!

![](/images/htb/montervede/4.png)

### SMB Enumeration
Dengan menggunakan user yang kita dapatkan tadi, kita mencoba untuk enumerasi service SMB dengan menggunakan tools smbclient dan didapatkan credential user lain di folder share **users$/mhope**.

![](/images/htb/montervede/5.png)

![](/images/htb/montervede/6.png)


## Gaining Access

### Initial Shell & Get User.txt

Setelah mendapatkan credential ke dua, kita coba validasi apakah user ini bisa digunakan untuk remote shell target atau tidak?

![](/images/htb/montervede/7.png)

![](/images/htb/montervede/8.png)

Ternyaata bisa untuk login dan didapatkan flag user.txt : **4961976bd7d8f4eeb2ce3705e2f212f2**


## Privilege Escalation

Pertama kita check user mhope termasuk member dari group apa, dan didapatkan user mhope masuk dalam group Azure Admins. Merasa tidak familiar dengan Azure, kita coba gali lagi lebih dalam apakah ada sesuatu yg bisa kita abuse.

![](/images/htb/montervede/9.png)

Setelah melakukan enumerasi yang cukup melelahkan, akhirnya tertuju pada proses yang saat ini sedang berjalan. Ada beberapa proses yang cukup menarik yang berkatian dengan Active Directory. 

![](/images/htb/montervede/10.png)

Proses **Microsoft.Identity.AadConnect.Health.AadSync.Host**  dan **Microsoft.Identity.Health.AadSync.MonitoringAgent.Startup** adalah  proses yang berkaitan dengan Azure Active Directory (Azure AD) Connect.  

FYI, salah satu fitur dari Azure AD Connect ada alah Password Hash Synchronisation , adalah sign-in method yang melakukan sinkrosinasi hash password yang dimiliki user on-premise dengan yang ada di Azure AD. 

![](/images/htb/montervede/11.png)

### Extract Credentials

Azure AD Connect dapat menerima data dari Active Directory yang kemudian akan diforward ke Azure AD. Proses sinkronisasi ini menggunakan fungsi Azure AD Sync yang secara default menggunakan database local bernama ADSync. Nah didalam database ini terdaoat metadata dan konfigurasi yang diencrypt. Menggunakan tools yang dibuat oleh CyberVaca, dengan sedikit memudahkan kita untuk mendapatkan credential yang ada dalam database. 

![](/images/htb/montervede/12.png)

    [+] Domain:  MEGABANK.LOCAL
    [+] Username: administrator
    [+] Password: d0m@in4dminyeah!


### Get Flag Root.txt

![](/images/htb/montervede/13.png)

Root Flag : **12909612d25c8dcf6e5a07d1a804a0bc**

Proses penyeleasaian mesin ini tidak semudah yang ada ditulisan, tulisan ini hanya menjelaskan alur yang benar setelah menempuh kegagalan-kegagalan yang tidak disebutkan. Terimakasih sudah membaca, jika dirasa tulisan ini bermanfaat, silahkan share. Semoga manfaat ini terus berlanjut!

Referensi :
- [Azure AD Connect for Red Teamers](https://blog.xpnsec.com/azuread-connect-for-redteam/)
- [Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-azure-ad-connect)
- [Azure-ADConnect.ps1](https://github.com/Hackplayers/PsCabesha-tools/blob/master/Privesc/Azure-ADConnect.ps1)

