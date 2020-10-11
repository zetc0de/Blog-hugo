---
layout: post
title: "Ansible Provisioning With Vagrant" 
date: 2018-07-26
tags: [ "Vagrant","Ansible","Provisioning" ]
categories: [ "DevOps" ]
---
![](/images/vagrant/vagrant04/ansible.png)

## Introduction

Setelah sebelumnya kita belajar bagaimana melakukan [Bash Provisioning Dengan Vagrant](http://localhost:1313/post/2018-06-24-bash-provisioning-with-vagrant/), sekarang kita akan melanjutkan belajar provisioning menggunakan ansible.

Ansible adalah sebuah provisioning tool yang dikembangkan oleh RedHat. Dimana kamu dapat mencatat setiap proses deployment ataupun konfigurasi yang biasa dilakukan berulang - ulang terhadap beberapa server.

## Create Vagrantfile
Mulailah dengan memuat vagrantfile:

```
  config.vm.define "vm02" do |vm02|
      vm02.vm.box = "ubuntu/xenial64"
      vm02.vm.hostname = "vm02"
      vm02.vm.network :privat_enetwork, ip: "10.3.3.11"
      vm02.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
      end
      vm02.vm.network "forwarded_port", guest: 80, host:8002
      vm02.vm.provision "ansible" do |x|
	x.verbose = "v"
	x.playbook = "provision/playbook.yml"
      end
  end
```

**Penjelasan :**
- vm02 : Nama vm kita
- vm02.vm.box : Menggunakan box ubuntu/xenial64
- vm02.vm.hostname : Untuk setting hostname
- vm02.vm.network : Setting network dengan ip private dan prot forwarding.
- vm02.vm.provision : Provisioning menggunakna ansible.

Download file provisioning ansible [disini](/files/provision.zip), lalu extrak dan letakkan pada directory yang sama dengan vagrantfiile, kemudian jalankan vm02 dengan perintah `vagrant up`.

![](/images/vagrant/vagrant04/cek.png)

## Provisioning 
Pastikan vm02 dalam kondisi running, untuk provisioning kali ini kita akan coba install webserver apache dan build web dengan jinja2, silahkan oprek ansiblenya dan disesuaikan dengan kebutuhan. Untuk membuktikan hasil proovisioningnya, editlah variable nama pada file `provision/roles/apache/vars/main.yml` dengan nama anda. Untuk menjalankan provisioningnya gunakan perintah:

    vagrant provision vm02
 

![](/images/vagrant/vagrant04/provisioning.png)

Dari provisioning diatas kita akan mendapatkan result sebagai berikut:

![](/images/vagrant/vagrant04/result.png)