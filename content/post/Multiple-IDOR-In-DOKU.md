---
title: "Multiple IDOR in DOKU"
date: 2019-06-02T11:04:47+07:00
draft: false
tags: ["IDOR"]
categories: [ "Bug Bounty"]
---

## About DOKU
DOKU Wallet adalah sebuah layanan dompet elektronik (Wallet) yang dikeluarkan oleh PT. Nusa Satu Inti Artha yang dilengkapi dengan fitur penghubung ke kartu kredit dan uang elektronik/cash wallet. DOKU adalah penyedia layanan pembayaran elektronik dan manajemen resiko pertama di Indonesia.

## IDOR Vulnerability
Insecure Direct Object Reference terjadi saat aplikasi menyediakan akses langsung ke objek berdasarkan masukan yang diberikan pengguna. Hal ini memngkinkan hacker untuk mem-bypass autentifikasi standar dan mengakses catatan database maupun sumber daya lain.

Insecure Direct Object References memungkinkan penyerang untuk memotong otorisasi dan mengakses sumber daya secara langsung dengan memodifikasi nilai parameter yang digunakan untuk mengarahkan langsung ke objek. Sumber daya semacam itu bisa menjadi entri database milik pengguna lain, file dalam sistem, dan banyak lagi. Hal ini disebabkan oleh fakta bahwa aplikasi tersebut memasukkan input yang dipasok pengguna dan menggunakannya untuk mengambil objek tanpa melakukan pengecekan otorisasi yang memadai. [Source](https://www.linuxsec.org/2018/01/memahami-dan-menemukan-kerentanan.html)


## IDOR Pada DOKU (merchant.doku.com)
### IDOR Pada Fitur Add Bank
{{< youtube H5IcWSrBiuk >}}
### IDOR Pada Fitur Add Contact
{{< youtube wxbO-vsDbPk >}}
### IDOR Pada Fitur Transaction Report
{{< youtube vAF_N9Nwc20 >}}
### IDOR Pada Fitur Update Store Profile
{{< youtube pnfkxA4MlZw >}}

## Reference
- [Insecure Direct Object Reference IDOR](https://owasp.org/www-chapter-ghana/assets/slides/IDOR.pdf)