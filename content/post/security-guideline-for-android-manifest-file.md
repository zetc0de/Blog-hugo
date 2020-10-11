---
title: "Security Guideline for Android Manifest File"
date: 2019-08-11T12:50:13+07:00
draft: false
tags: ["Android"]
categories: [ "Mobile Pentesting" ]
---

## Overview
File Manifest memainkan peran penting untuk setiap aplikasi android. Dalam file ini developer android menentukan izin yang diperlukan aplikasi, tindakan yang dapat dilakukan aplikasi dan aktivitas umum lainnya. Dari sudut pandang keamanan, file manifest biasanya merupakan hal pertama yang akan diperiksa oleh pentester. Oleh karena itu daftar berikut dapat digunakan baik oleh developer dan penguji penetrasi untuk memastikan bahwa file Manifest tidak mengandung kesalahan konfigurasi yang dapat dimanfaatkan oleh penyerang.


## Debug Mode

Tag debug mendefinisikan apakah aplikasi dapat di-debug atau tidak. Jika aplikasi tersebut dapat di-debug maka dapat memberikan banyak informasi kepada penyerang. Aplikasi android yang tidak dalam status production diharapkan memiliki atribut ini, disetel ke true untuk membantu developer, namun sebelum rilis aplikasi, tag ini harus disetel ke false.

    <application
    android:debuggable="false"
    </application>

## BackUp Flag

Pengaturan ini menentukan apakah data aplikasi dapat dibackup dan direstore oleh pengguna yang telah mengaktifkan debugging usb. Oleh karena itu aplikasi yang menangani dan menyimpan informasi sensitif seperti detail kartu, kata sandi dll harus menetapkan pengaturan ini menjadi false untuk mencegah risiko tersebut.

    <application
    android:allowBackup="false"
    </application>

## External Storage

Aplikasi yang memiliki izin untuk menyalin data ke penyimpanan eksternal harus ditinjau untuk memastikan bahwa tidak ada informasi sensitif yang disimpan.

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
 

## Permissions

Atribut android:protectionLevel menentukan prosedur yang harus diikuti oleh sistem sebelum memberikan izin ke aplikasi yang memintanya. Tujuan adanya permission adalah untuk melindungi privasi user android, aplikasi android harus meminta izin kepada user untuk memberikan akses ke data user yang sensitif seperti kontak dan SMS. Ada empat nilai yang dapat digunakan dengan atribut ini: 

- normal
- dangerous
- signature
- signatureOrSystem

Semua izin permintaan aplikasi harus ditinjau untuk memastikan bahwa mereka tidak menimbulkan risiko keamanan.

    <permission>
    android:protectionLevel="signature"
    </permission>

## Application component

Tergantung pada fungsionalitas sebuah aplikasi dapat menjalankan service, melakukan activity, menerima content dari sumber lain atau menerima intens oleh telepon atau oleh aplikasi lain. Ada empat komponen aplikasi:

- Activities
- Services
- Content Providers
- Broadcast Receivers

Activities, Services, Content Providers and Broadcast Receivers semua dapat di-export. Oleh karena itu, mereka semua harus ditinjau bahwa mereka tidak melakukan tindakan sensitif apa pun dan bahwa mereka dilindungi oleh izin yang sesuai. Karena jika tidak, informasi dapat diekspos kepada pihak ketiga yang berbahaya.  Berikut menunjukkan bagaimana reciever didefinisikan dalam file manifes:

    <receiver
    android:exported="true"
    android:name="string"
    android:permission="string"
    </receiver>

## Intents

Intens dapat digunakan untuk menjalankan sebuah acticity, untuk mengirimkan ke broadcast receiver, dan untuk berkomunikasi dengan background service. Intents message harus ditinjau untuk memastikan bahwa itu tidak mengandung informasi sensitif yang dapat diintercept.

    <intent-filter>
    <action android:name="string" />
    <category android:name="string" />
    </intent-filter>


## Kesimpulan
Dengan mengikuti panduan ini, developer akan tahu cara menerapkan file Manifest dengan benar untuk menghilangkan risiko keamanan. Dari sisi lain penguji penetrasi akan tahu bagaimana melakukan review manual dari file manifest android.

## References
- [Security Guidelines for Android Manifest Files](https://pentestlab.blog/2017/01/24/security-guidelines-for-android-manifest-files/)