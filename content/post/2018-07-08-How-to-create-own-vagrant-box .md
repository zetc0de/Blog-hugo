---
layout: post
title: "How To Create Own Vagrant Box" 
date: 2018-07-30
tags: [ "Vagrant" ]
---
![](/images/vagrant/vagrant05/vagrantbox.webp)

## Introduction
Setelah beberapa kali kita mencoba menggunakan vagrant dari vagrantbox ubuntu/xenial64 yang jika kita sadari, kita menggunakan pure box dari ubuntu, alias masih kosong blong bawaan ubuntu. Mungkin kita ingin beberapa tools yg terinstall pada vm kita, seperti tools build-essential,vim,atau tools-tools keseharina kita yang tidak ada pada OS bawaan ubuntu. 

Untuk mengakali hal itu, kita dapan membuat vagrantbox milik kita sendiri. Dengan tool-tool kesayangan kita, kita seperti dapat membuat OS dengan tools sesuai keinginan kita sendiri. 

## Install Base OS
Untuk memulainya, karena kita akan menggunakan ubuntu 18.04 sebagai base box ini, maka buatlah VM dan install OS Ubuntu 18.04. Lalu masuk kedalam VM tersebut, dan buatlah user baru di vm tersebut.

![](/images/vagrant/vagrant05/setuser.png)

Jika sudah, login ke user tersebut dan cek versi ubuntunya:
```
cat /etc/lsb_release
```

![](/images/vagrant/vagrant05/lsb-release.png)

## Port Forwarding dari Virtualbox
- Klik Ubuntu servernya - klik 'Settings' button.
- Pada bagian 'Adapter 1' pastikan jenis network 'NAT'.
- Klik option 'Advanced' di bawah dan klik 'Port Forwarding'.
- Isikan detail Port-Forwarding :
```
  Name: ssh
  Protocol: TCP
  Host IP: kosongkan
  Host Port: 2222
  Guest IP: kosongkan
  Guest Port: 22
```

Klik tombol 'Ok', dan 'Ok' lagi untuk save.

![](/images/vagrant/vagrant05/setnat.png)

## Konfigurasi Dan Install Paket yang dibutuhkan

Pada dasarnya membuat box sendiri itu karena kebutuhan kita terhadap beberapa aplikasi yang tidak ada pada box lain, atau kita ingin membuat costum box dengan aplikasi yang kita inginkan saja. 

### Install Aplikasi
```
sudo apt update
sudo apt install vim git unzip net-tools wget
```
### Setup Root Password
```
sudo passwd root
```
### Setup user 'vagrant'
```
useradd -m -s /bin/bash vagrant
passwd vagrant
```
Beri password 'vargant'.

### Konfigurasi sudoers user vagrant 
```
sudo vim /etc/sudoers.d/vagrant
```
Paste konfigurasi berikut.
```
# add vagrant user
vagrant ALL=(ALL) NOPASSWD:ALL
```
### Testing Vagrant user.
```
su - vagrant
sudo su
```

![](/images/vagrant/vagrant05/sudosu.png)

## Konfigurasi key dan SSH

### Download vagrant key
```
mkdir -p /home/vagrant/.ssh
chmod 0700 /home/vagrant/.ssh
wget --no-check-certificate \
          https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub \
          -O /home/vagrant/.ssh/authorized_keys
chmod 0600 /home/vagrant/.ssh/authorized_keys
chown -R vagrant /home/vagrant/.ssh
```

![](/images/vagrant/vagrant05/vagrantpub.png)

### Konfigurasi ssh login dengan key.
```
cd /etc/ssh/
vim sshd_config
```
Ubah line seperti berikut.
```
AuthorizedKeysFile %h/.ssh/authorized_keys
```

### Update dan upgrade Paket
```
sudo apt update
sudo apt upgrade -y
```

## Installasi Guest Tools

### Install Paket dependensi.
```
export versi=$(uname -r)
sudo apt-get install -y gcc build-essential linux-headers-$versi
```

![](/images/vagrant/vagrant05/guest.png)

### Download ISO VirtualBox Guest Additional ke Komputer Anda

https://download.virtualbox.org/virtualbox/5.2.18/VBoxGuestAdditions_5.2.18.iso

### Mount dan Install VirtualBox guest additon

Klik ubuntu server yang masih 'running', dan klik menu 'Devices'.
Klik 'Insert Guest Addition CD Image..'

Masuk ke ubuntu server dan mount.
```
sudo mount /dev/cdrom /mnt
cd /mnt
sudo ./VBoxLinuxAdditions.run
```

![](/images/vagrant/vagrant05/guestad.png)

### Clean the Box

```
sudo apt autoremove
sudo apt clean

sudo dd if=/dev/zero of=/EMPTY bs=1M
sudo rm -f /EMPTY

history -c
tekan 'CTRL+d' untuk exit dari terminal.
```

## Packaging Box Vagrant

Balik ke terminal komputer.
```
vagrant package --base zetc0de-ubuntu-vm
```

![](/images/vagrant/vagrant05/packaging.png)

## Import Box and Testing

```
vagrant box add ubuntu-zetc0de-1804 package.box

mkdir ~/Vagrant/box-ku/
cd  ~/Vagrant/box-ku/

vagrant init ubuntu-zetc0de-1804
vagrant up
```
Coba ssh vm tersebut.

```
vagrant ssh
```
