---
title: "IDOR Delete Project Pada Progrez Cloud"
date: 2020-08-11T16:52:45+07:00
draft: false
tags: ["IDOR"]
categories: [ "Bug Bounty"]
---

![](/images/bugbounty/progrez/intro.png)

## About Progrez Cloud
Progrez Cloud adalah aplikasi untuk menegement project yang dapat diakses melalui web browser maupun mobile application , saat ini Progres Cloud memiliki beberapa fitur diantaranya :
- Manage Projects
- Monitoring Website
- Global Chat
- Manage Monthly Outcome
- Personal Blog
- etc

Anda dapat mengakses Progrez Cloud pada alamat [https://progrez.cloud/](https://progrez.cloud/) atau jika anda menggunakan smartphone android anda dapat mengunduh aplikasinya di Play Story [Progrez.Cloud](https://play.google.com/store/apps/details?id=cloud.progrez.app)

## About IDOR Vulnerability
Insecure Direct Object Reference (IDOR) terjadi saat aplikasi menyediakan akses langsung ke objek berdasarkan masukan yang diberikan pengguna. Hal ini memngkinkan hacker untuk mem-bypass autentifikasi standar dan mengakses catatan database maupun sumber daya lain.

IDOR memungkinkan penyerang untuk memotong otorisasi dan mengakses sumber daya secara langsung dengan memodifikasi nilai parameter yang digunakan untuk mengarahkan langsung ke objek. Sumber daya semacam itu bisa menjadi entri database milik pengguna lain, file dalam sistem, dan banyak lagi. Hal ini disebabkan oleh fakta bahwa aplikasi tersebut memasukkan input yang dipasok pengguna dan menggunakannya untuk mengambil objek tanpa melakukan pengecekan otorisasi yang memadai.

## 3 Jenis Privacy Project 
Pada dashboard Progrez terdapat menu project dimana kita dapat membuat project atau join ke project orang lain. 

Diketahui bahwa project memiliki 3 jenis privacy :
1. Private (Team Only) 
2. Public (Open) - Anyone can join
3. Public (Close)- Read Only

![](/images/bugbounty/progrez/1.png)

Pada project yang memiliki privacy 1 (Private) maka orang lain tidak dapat melihat project tersebut, berbeda dengan privacy 2 dan 3, setiap project yang memiliki privacy 2 dan 3 maka setidaknya orang lain dapat melihat detail project tersebut.


## IDOR Delete Project
Karena platform ini berjalan pada environment production maka rasanya tidak etis jika menggunakan data production sebagai bahan uji penetrasi, oleh karena itu untuk melakukan uji penetrasi ini dibutuhkan setidaknya 2 akun tester yang didapat dengan memanfaatkan fitur Signup.

2 akun ini akan bertindak sebagai : 
- 1 akun sebagai attacker 
- 1 akun sebagai victim/target. 

Celah keamanan IDOR ini terdampak pada fitur delete project yang mengakibatkan attacker dapat menghapus setiap project yang memiliki privacy 2 dan 3.

## Proof Of Concept
1. Pilih salah satu public project yang terdapat pada menu Join Project (Pilih project dummy yang dibuat akun victim)
2. Ketika memilih public project, maka project id akan terlihat pada request body
![](/images/bugbounty/progrez/2.png)
3. Lakukan intercept pada proses delete project milik akun attacker
4. Kemudian ganti project_id milik attacker dengan project_id milik victim
![](/images/bugbounty/progrez/3.png)
5. Maka secara tidak langsung project victim akan terhapus
![](/images/bugbounty/progrez/4.png)

## Timeline
- 11 Aug 2020 14:18 : Bug Reported
- 11 Aug 2020 16:03 : Triaged
- 11 Aug 2020 16:16 : Bug Fixed
- 11 Aug 2020 16:29 : Listed On Hall Of Fame [(HOF)](https://progrez.cloud/hall-of-fame/)


## References
- [OWASP - Insecure Direct Object Reference IDOR](https://owasp.org/www-chapter-ghana/assets/slides/IDOR.pdf)
- [LinuxSec - Memahami dan Menemukan Kerentanan Insecure Direct Object Reference](https://www.linuxsec.org/2018/01/memahami-dan-menemukan-kerentanan.html)