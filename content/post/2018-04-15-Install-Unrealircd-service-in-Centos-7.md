---
layout: post
title: "How To Install Unrealircd In Centos 7"
date: 2018-04-15
tags: [ "IRC","Unrealircd","Centos 7" ]
categories: [ "Linux Administrator" ]
---

![IRC](/images/centos7/irc/unrealircd/logo.jpg)

Halo, kali ini kita akan menginstall Unrealircd di OS Centos 7. 

## Introduction
**UnrealIRCd** adalah daemon IRC open source, awalnya berbasis pada DreamForge, dan tersedia untuk sistem operasi Unix-like dan Windows. Sejak awal pengembangan pada UnrealIRCd c. Mei 1999, banyak fitur baru yang ditambahkan dan dimodifikasi, termasuk fitur keamanan yang canggih dan perbaikan bug, dan Unrealircd menjadi server yang populer.

## Installation
Oke langsung saja,seperti biasa kita harus install Development Tools dulu dan beberapa tools yang diperlukan, 

> yum group install "Development Tools"
> 
> yum install make cmake openssl openssl-devel 

Lalu kita download source Unreal dan mengekstraknya:

>
>wget https://www.unrealircd.org/unrealircd4/unrealircd-4.0.17.tar.gz
>
>tar -zxvf unreal*
>
>cd unreaal*

Lalu kita install Unreal dengan perintah berikut:

>./Config && make && make install 
>

Press Enter-enter saja sampai selesai, nanti hasil installasi akan terletak di home directory.

Salin file example konfigurasi ke `~/unrealircd/conf/`
>
>cp ~/unrealircd/conf/examples/example.conf ~/unrealircd/conf/unrealircd.conf
>

Kemudian konfigurasi file unrealircd.conf sesuai kebutuhan, aku konfig bagian-bagian dibawah ini :
```
 59 me {
 60         name "irc.random.io";
 61         info "IRC RANDOM";
 62         sid "001";
```

```
 68 admin {
 69         "zetc0de";
 70         "zetc0de";
 71         "zetc0de@gmail.com";
```

```
144 oper zetc0de {
145         class opers;
146         mask *@*;
147         password "passwordkamu";
```

```
173 /* Standard IRC port 6667 */
174 listen {
175         ip *;
176         port 6667;
177 };
178 
179 /* Standard IRC SSL/TLS port 6697 */
180 listen {
181         ip *;
182         port 6697;
183         options { ssl; };
184 };
185 
186 /* Special SSL/TLS servers-only port for linking */
187 listen {
188         ip *;
189         port 6900; /*port untuk konekin ke service Anope*/
190         options { ssl; serversonly; };
191 };
```

```
376 /* Network configuration */
377 set {
378         network-name            "RANDOM";
379         default-server          "irc.random.io";
380         services-server         "services.random.io";
381         stats-server            "stats.random.io";
```

```
393         cloak-keys {
394                "YeK47v7oa4IYH1e64y14QW6Vx6ALN4GP1p0DFlQ8CGWCe0K6jyo3cCw";
395                "3C5scCBJPDkLYSC1PID1IUjo3344K8W8p1u04Dm2X5JnP5D04JevE6OH5";
396                "dubhE01QGEOlm317i836vLme6Wcov3MI71G43iM7w2I6t12f7N";
```

```
402 set {
403         kline-address "zetc0de.id@gmail.com"; /* e-mail or URL shown when a use    r is banned */

```
Untuk gencloak bisa menggunakan perintah ini:
> ./unrealircd gencloak

Anda dapat melihat full file konfigurasinya [disini](/files/unrealircd.conf)

Sebelum kita jalankan Unrealircd nya, kita buka port sesuai port di konfigurasi `listening` nya:

>firewall-cmd --add-port=6697/tcp --permanent
>
>firewall-cmd --add-port=6667/tcp --permanent
>
>firewall-cmd --reload
>

Lalu kita start 
> ./unrealircd start
> 

~Sekian
