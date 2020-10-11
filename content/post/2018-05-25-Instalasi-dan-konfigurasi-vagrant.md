---
layout: post
title: "Instalasi dan Konfigurasi Dasar Vagrant" 
date: 2018-05-25 
tags: [ "Vagrant" ]
categories: [ "DevOps" ]
---


![](/images/vagrant/vagrant01/logo.jpg)

## Introduction

Vagrant adalah sebuah software yang menggunakan teknologi virtual machine dimana kita dapat membuat lingkungan development secara portable, konsisten dan lebih fleksible.

Saat ini virtualisasi menjadi penunjang bagi lingkungan development software secara modern. Dalam satu laptop kita bisa memasang berbagai sistem operasi virtual yang disesuaikan dengan server kita, sehingga kita bisa memisahkan configurasi laptop kita dengan konfigurasi server. 

Banyak keuntungan ketika kita menggunakan vagrant,sebagai developer sofrware akan sangat memudahkan jika menggunakan vagrant sebagai development environment yang terisolasi di virtual. 

## Installation
Untuk memasang vagrant, caranya sangat mudah. Jika anda menggunakan OS keluarga UNIX, anda dapat memasangnya menggunakan package manager. Misalkan anda menggunakan Ubuntu:


    sudo apt-get install virtualbox
    sudo apt-get install vagrant

## Vagrant Box
Ketika vagrant dalam kondisi fresh install, maka kita bekum meiliki box/image untuk kita gunakan pada vagrant. Untuk itu kita download vagrant boxnya dahulu, anda dapat melihat-lihat vagrant box [disini](https://app.vagrantup.com/boxes/search). Saya asumsikan anda akan mendownload box ubuntu xenial:


    vagrant box add ubuntu/xenial64


Anda dapat melihat box yang anda miliki dengan perintah : `vagrant box list`

![](/images/vagrant/vagrant01/boxlist.png)

## Membuat VM Dengan Vagrant Init
Untuk memulainya, mari kita membuat directory,silahkan buat directory ditempat yang anda suka, dan jalankan `vagrant init ubuntu/xenial64` atau dengan box yang anda inginkan :

    mkdir -p ~/Vagrant/myubuntu
    cd ~/Vagrant/myubuntu
    vagrant init ubuntu/xenial64

![](/images/vagrant/vagrant01/vagrant-init.png)

Dengan perintah diatas, maka akan membuat vagrantfile, dengan vagrantfile tersebut kita dapat mengkofigurasikan VM(Virtual mechine) sesuai kehendak kita. 

![](/images/vagrant/vagrant01/konf.png)

Bisa anda lihat diatas, kita menggunakan box ubuntu/xenial64, dengan private network dan menggunakan memory sebesar 512, tinggal sesuaikan dengan kebutuhan. Ada beberapa variable yang dapat anda konfigurasikan, bebrapa diantaranya:

- config.vm.box : Inisialisasi Vagrantbox
- config.vm.hostname : Konfigurasi hostname (default hostaname adalah vagrant)
- config.vm.network : KOnfigurasi network, anda dapat meset networknya menggunakan private_network maupun public_network)
- config.vm.provision : Untuk kebutuhan provisioning

## Menjalankan VM
Jika sudah dikonfigurasi, maka jalankan vm pertama kita:

    vagrant up
    vagrant status

![](/images/vagrant/vagrant01/up.png)

## Masuk Console VM Dengan Vagrant SSH
Anda dapat melihat vm kita dalam kondisi running, artinya kita sudah menjalankan vm tersebut. Untuk mengaksesnya menggunakan:

    vagrant ssh

![](/images/vagrant/vagrant01/ssh.png)

Maka anda sudah dapat menjalankan perintah-perintah Ubuntu pada VM tersebut.
 