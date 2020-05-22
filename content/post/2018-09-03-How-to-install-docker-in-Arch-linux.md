---
layout: post
title: "How To Install Docker In Arch Linux"
date: 2018-09-03
tags: [ "Docker" ]
---
![](/images/docker/docker01/docker.png)
## Introduction

Docker adalah salah satu platform yang dibangun berdasarkan teknologi container. Docker merupakan sebuah project open-source yang menyediakan platform terbuka untuk developer maupun sysadmin untuk membangun,mengemas,dan menjalankan aplikasi dimanapun sebagai sebuah wadah(container) yang ringan. Dengan sangat populernya docker, sebagian orang sering menganggap docker adalah sebutan lain dari container. (codepolitan)

## Instalasi
Dikarenakan saya menggunakan OS Arch Linux, maka disini saya akan menyajikan langkah-langkah ketika ingin menginstall docker di OS Arch Linux, anda dapat menyesuaikan dengan OS masing-masing.

### Enable The Loop Module
Cek module loop:
```
sudo su
lsmod | grep loop
```
Jika tidak ada result apapun artinya module loop belum terload,mari kita enable module loop:
```
tee /etc/modules-load.d/loop.conf <<< "loop"
modprobe loop
```

Cek kembali dan pastikan module loop sudah enabled.

### Install docker
Anda dapat menginstall docker melalui repository comunity (AUR), biasanya bernama `docker-git`, tapi disini saya akan install melalui repository arch:
```
sudo pacman -S docker
```

![](/images/docker/docker01/install.png)

## Start Docker
Sebelum memulai docker, anda harus start service docker terlebih dahulu, dan pastikan docker dalam kondisi running:
```
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```

![](/images/docker/docker01/status.png)

Jika sudah running, kita coba jalankan `docker info` dan didapatkan:

![](/images/docker/docker01/info.png)

Artinya docker hanya dapat berjalan dalam kondisi root. Untuk menjalankan docker dalam mode user biasa. maka kita harus menambahkan user tersebut kedalam docker group:
```
sudo su
gpasswd -a user docker
```
`Note: user diganti dengan username anda`

![](/images/docker/docker01/group.png)

Jangan lupa untuk re-login untuk apply perubahan.

Maka ketika kita akses `docker info` dengan user biasa akan mendapatkan result seperti ini:

![](/images/docker/docker01/info2.png)

## Testing docker on Arch Linux
Coobalah dengan menjalankan perintah-perintah docker, anda dapat melihatnya dengan perintah:
```
docker -h
```
Akan ada banyak command docker, salah satunya adalah `docker pull`, docker pull digunakan untuk mendownload docker images dari registry ke client:
```
docker pull ubuntu:18.04
```

![](/images/docker/docker01/pull.png)

Perintah diatas akan mendownload images ubuntu dengan tag 18.04, anda dapat melihat berbagai images di [Docker Hub](https://hub.docker.com).

Untuk melihat semua images yang kita miliki, gunakan perintah:
```
docker images
```

![](/images/docker/docker01/images.png)


