---
layout: post
title: "Create Multiple VM with one Vagrantfile" 
date: 2018-06-10
tags: [ "Vagrant" ]
categories: [ "DevOps" ]
---
![](/images/vagrant/vagrant02/Vagrant.png)
## Introduction
Menggunakan vagrant untuk kebutuhan virtualisasi sangat membantu pekerjaan developer maupun system engineer. Dengan vagrant kita dapat membuat virtual environtment dengan mudah. 

Masalahnya, terkadang kita membutuhakan lebih dari satu VM untuk kebutuhan testing maupun untuk kebutuhan yang lain, dengan vagrant kita dapat membuat multipele VM hanya dengan sekali inisialisasi vagrantfile.

## Membuat 2 VM Dengan 1 Vagrantfile
Pada kesempatan kali ini kita akan membuat 2 vm menggunakan 1 vagrantfile. Untuk memulainya, buatlah sebuah direktory untuk vagrantfile kita:
```
mkdir ~/Vagrant/multiple-vm
cd Vagrant/multiplr-vm
```

Lalu buatlah sebuah Vagrantfile menggunakan text editor kesayangan anda:

```
# Inisialisasi Vagrantfile configuration
Vagrant.configure("2") do |config|

  # definisikan vm01
  config.vm.define "vm01" do |vm01|
      vm01.vm.box = "ubuntu/xenial64"
      vm01.vm.hostname = "vm01"
      vm01.vm.network :private_network, ip: "10.3.3.10"
  end

  # definisikan vm02
  config.vm.define "vm02" do |vm02|
      vm02.vm.box = "ubuntu/xenial64"
      vm02.vm.hostname = "vm02"
      vm02.vm.network :private_network, ip: "10.3.3.11"
      vm02.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
      end
  end

end
```
Penjelasan konfigurasi diatas:
- Kita deinisikan vm pertama dengan nama vm01, dan vm kedua dengan nama vm02.
- Kita sama-sama menggunakan box ubuntu/xenial64
- Menggunakan hostname vm01 untuk vm pertama dan vm02 untuk vm kedua.
- Menggunakan private network dengan ip 10.3.3.10 untuk vm01 dan ip 10.3.3.11 untuk vm02.
- Sama-sama menggunakan RAM 512 mb.

## Menjalankan VM 
Setelah itu save dengan nama Vagrantfile, lalu jalankan dengan perintah:
```  
vagrant up
vagrant status
```

![](/images/vagrant/vagrant02/status.png)

Maka akan terlihat status dari 2 vm kita sedang dalam kondisi running. Kita bisa masuk kedalam vm nya dengan perintah:
```
vagrant ssh vm01
vagrant ssh vm02
```

![](/images/vagrant/vagrant02/ssh.png)

## Testing Network Antar VM
Tinggal sesuaikan dengan nama vm nya, maka anda kan masuk kedalam mesin virtual.

Kita coba untuk saling melakukan `ping` antar vm kita:

![](/images/vagrant/vagrant02/ping.png)

