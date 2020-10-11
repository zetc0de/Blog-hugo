---
title: "HTB Writeup - Nest"
date: 2020-06-07T02:39:53+07:00
draft: false
tags: [ "Windows"]
categories: [ "HTB"]
---

![](/images/htb/nest/nest.png)

## Overview
Mesin ini dibuat oleh [VbScrub](https://www.hackthebox.eu/home/users/profile/158833) , sistem operasi yang digunakan adalah Windows dengan level kesulitan **Easy** dan memiliki point sebesar 20 point. Untuk menyelesaikan mesin ini anda harus memiliki skill enumeration yang bagus,dan sedikit memahami bahasa pemograman visual basic. Mesin ini direlease pada tanggal 25 Januari 2020 dan retired pada tanggal 5 Juni 2020.

## Scanning & Enumeration

### Port scanning

Pertama kita akan memulainya dengan melakukan scanning terhadap target menggunakan nmap, dari hasil scanning kita mendapatkan informasi berupa port ataupun service yang berjalan dan dapat diakses dari external network. 

    nmap -p- 10.10.10.178 -v -Pn

dan didapatkan 2 port terbuka, yaitu:
- 445 adalah port SMB
- 4386 adalah port service HQK Reporting

![](/images/htb/nest/1.png)

### Port SMB

Nmap memberikan informasi bahwa port 445 yang digunakan service SMB terbuka, sehingga kita dapat melakukan enumerasi lebih jauh terhadap service ini. Disini kita menggunakan smbclient untuk melakukan enumerasi dan didapatkan beberapa shared folder didalamnya.

![](/images/htb/nest/2.png)

Kemudian didapati bahwa folder **Data** memiliki akses _Read_, sehingga kita dapat membaca isi dari folder tersebut. Berikut sedikit penjelasan beberapa command smbclient yang digunakan :
- mask “” : digunakan ketika proses mget atau mput secara recursive
- recursive ON : mengaktifkan mode recursive
- prompt OFF : menonaktifkan mode prompt 
- lcd smb : setting destinasi folder yang ada pada local
- mget * : mengambil seluruh file/folder yang ada

dari file yang berhasil didapat, kita mendapatkan informasi credential **TempUser:welcome2019**

![](/images/htb/nest/3.png)

![](/images/htb/nest/4.png)

    We would like to extend a warm welcome to our newest member of staff, <FIRSTNAME> <SURNAME>
    
    You will find your home folder in the following location: 
    \\HTB-NEST\Users\<USERNAME>
        
    If you have any issues accessing specific services or workstations, please inform the 
    IT department and use the credentials below until all systems have been set up for you.
        
    Username: TempUser
    Password: welcome2019
        
        
    Thank you

Kemudian dengan credential yang kita dapatkan tadi, kita coba akses kembali shared folder Data dan mengambil file dengan credential tersebut.

![](/images/htb/nest/5.png)

Pada file **Data/RU Scanner/RU_config.xml** ditemukan credential user c.smith, seperti base64 bukan? namun setelah mencoba didecode dan coba login smb dengan password hasil decode ternyata failed :(

![](/images/htb/nest/6.png)

## Decrypt C.Smith Password

Setelah stuck beberapa waktu, didapatkan file **Data/NotepadPlusPlus/config.xml** yang memberikan informasi bahwa terdapat folder **Carl** didalam \\**HTB-NEST\Secure$\IT**. Kita coba masuk folder IT/Carl/ dan ternyata bisa diakses!

![](/images/htb/nest/7.png)

![](/images/htb/nest/8.png)

```
smb: \> cd IT/Carl/
smb: \IT\Carl\> mget *
getting file \IT\Carl\Docs\ip.txt of size 56 as ip.txt (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)
getting file \IT\Carl\Docs\mmc.txt of size 73 as mmc.txt (0.5 KiloBytes/sec) (average 0.4 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\ConfigFile.vb of size 772 as ConfigFile.vb (6.1 KiloBytes/sec) (average 2.0 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\Module1.vb of size 279 as Module1.vb (2.0 KiloBytes/sec) (average 2.0 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\My Project\Application.Designer.vb of size 441 as Application.Designer.vb (3.4 KiloBytes/sec) (average 2.3 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\My Project\Application.myapp of size 481 as Application.myapp (3.6 KiloBytes/sec) (average 2.5 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\My Project\AssemblyInfo.vb of size 1163 as AssemblyInfo.vb (8.8 KiloBytes/sec) (average 3.3 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\My Project\Resources.Designer.vb of size 2776 as Resources.Designer.vb (21.2 KiloBytes/sec) (average 5.4 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\My Project\Resources.resx of size 5612 as Resources.resx (43.5 KiloBytes/sec) (average 9.4 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\My Project\Settings.Designer.vb of size 2989 as Settings.Designer.vb (22.5 KiloBytes/sec) (average 10.7 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\My Project\Settings.settings of size 279 as Settings.settings (2.1 KiloBytes/sec) (average 9.9 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\RU Scanner.vbproj of size 4828 as RU Scanner.vbproj (37.1 KiloBytes/sec) (average 12.1 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\RU Scanner.vbproj.user of size 143 as RU Scanner.vbproj.user (1.1 KiloBytes/sec) (average 11.3 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\SsoIntegration.vb of size 133 as SsoIntegration.vb (1.0 KiloBytes/sec) (average 10.6 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner\Utils.vb of size 4888 as Utils.vb (38.8 KiloBytes/sec) (average 12.3 KiloBytes/sec)
getting file \IT\Carl\VB Projects\WIP\RU\RUScanner.sln of size 871 as RUScanner.sln (6.8 KiloBytes/sec) (average 12.0 KiloBytes/sec)
```


Setelah mendownload semua file yang ada pada folder **Carl**, didapatkan beberapa file yang merupakan project visual basic. File Module1.vb menunjukkan bahwa main program akan meload file RU_config.xml yang kita dapatkan tadi, termasuk didalamnya credential user c.smith. Terlihat jelas bahwa fungsi DecryptString terdapat pada class Utils, yang mana fungsi ini digunakan untuk mendecrypt password milik c.smith yang tadi sempat kita kira itu adalah base64 encoded =D

![](/images/htb/nest/9.png)


Setelah membaca file Utils.vb, benar disana terdapat method DecryptString dengan parameternya termasuk key dan salt untuk proses dekripsi.
```
Imports System.Text
Imports System.Security.Cryptography
Public Class Utils

    Public Shared Function GetLogFilePath() As String
        Return IO.Path.Combine(Environment.CurrentDirectory, "Log.txt")
    End Function

    Public Shared Function DecryptString(EncryptedString As String) As String
        If String.IsNullOrEmpty(EncryptedString) Then
            Return String.Empty
        Else
            Return Decrypt(EncryptedString, "N3st22", "88552299", 2, "464R5DFA5DL6LE28", 256)
        End If
    End Function

    Public Shared Function EncryptString(PlainString As String) As String
        If String.IsNullOrEmpty(PlainString) Then
            Return String.Empty
        Else
            Return Encrypt(PlainString, "N3st22", "88552299", 2, "464R5DFA5DL6LE28", 256)
        End If
    End Function

    Public Shared Function Encrypt(ByVal plainText As String, _
                                   ByVal passPhrase As String, _
                                   ByVal saltValue As String, _
                                    ByVal passwordIterations As Integer, _
                                   ByVal initVector As String, _
                                   ByVal keySize As Integer) _
                           As String

        Dim initVectorBytes As Byte() = Encoding.ASCII.GetBytes(initVector)
        Dim saltValueBytes As Byte() = Encoding.ASCII.GetBytes(saltValue)
        Dim plainTextBytes As Byte() = Encoding.ASCII.GetBytes(plainText)
        Dim password As New Rfc2898DeriveBytes(passPhrase, _
                                           saltValueBytes, _
                                           passwordIterations)
        Dim keyBytes As Byte() = password.GetBytes(CInt(keySize / 8))
        Dim symmetricKey As New AesCryptoServiceProvider
        symmetricKey.Mode = CipherMode.CBC
        Dim encryptor As ICryptoTransform = symmetricKey.CreateEncryptor(keyBytes, initVectorBytes)
        Using memoryStream As New IO.MemoryStream()
            Using cryptoStream As New CryptoStream(memoryStream, _
                                            encryptor, _
                                            CryptoStreamMode.Write)
                cryptoStream.Write(plainTextBytes, 0, plainTextBytes.Length)
                cryptoStream.FlushFinalBlock()
                Dim cipherTextBytes As Byte() = memoryStream.ToArray()
                memoryStream.Close()
                cryptoStream.Close()
                Return Convert.ToBase64String(cipherTextBytes)
            End Using
        End Using
    End Function

    Public Shared Function Decrypt(ByVal cipherText As String, _
                                   ByVal passPhrase As String, _
                                   ByVal saltValue As String, _
                                    ByVal passwordIterations As Integer, _
                                   ByVal initVector As String, _
                                   ByVal keySize As Integer) _
                           As String

        Dim initVectorBytes As Byte()
        initVectorBytes = Encoding.ASCII.GetBytes(initVector)

        Dim saltValueBytes As Byte()
        saltValueBytes = Encoding.ASCII.GetBytes(saltValue)

        Dim cipherTextBytes As Byte()
        cipherTextBytes = Convert.FromBase64String(cipherText)

        Dim password As New Rfc2898DeriveBytes(passPhrase, _
                                           saltValueBytes, _
                                           passwordIterations)

        Dim keyBytes As Byte()
        keyBytes = password.GetBytes(CInt(keySize / 8))

        Dim symmetricKey As New AesCryptoServiceProvider
        symmetricKey.Mode = CipherMode.CBC

        Dim decryptor As ICryptoTransform
        decryptor = symmetricKey.CreateDecryptor(keyBytes, initVectorBytes)

        Dim memoryStream As IO.MemoryStream
        memoryStream = New IO.MemoryStream(cipherTextBytes)

        Dim cryptoStream As CryptoStream
        cryptoStream = New CryptoStream(memoryStream, _
                                        decryptor, _
                                        CryptoStreamMode.Read)

        Dim plainTextBytes As Byte()
        ReDim plainTextBytes(cipherTextBytes.Length)

        Dim decryptedByteCount As Integer
        decryptedByteCount = cryptoStream.Read(plainTextBytes, _
                                               0, _
                                               plainTextBytes.Length)

        memoryStream.Close()
        cryptoStream.Close()

        Dim plainText As String
        plainText = Encoding.ASCII.GetString(plainTextBytes, _
                                            0, _
                                            decryptedByteCount)

        Return plainText
    End Function

End Class
```

Karena saya tidak memiliki compiler VB, saya menggunakan compiler online di [dotnetfiddle](https://dotnetfiddle.net) , karena saya belum pernah melakukan scripting menggunakan VB, saya melihat beberapa basic tutorial seperti membuat variable, kemudian membuat sedikit baris program untuk memanggil method DecryptString untuk mendekripsi password milik c.smith, dan didapatkan passwordnya adalah **xRxRxPANCAK3SxRxRx**

![](/images/htb/nest/10.png)

## User Flag

Setelah mendapatkan credential c.smith, kita coba masuk service smb lagi dan mendapatkan beberapa file termasuk file flag user.txt

![](/images/htb/nest/11.png)

![](/images/htb/nest/12.png)

## Privilege Escalation

### HQK Reporting Service
Setelah mendownload file dari user c.smith, disana terdepat beberapa file diantaranya file exe, file txt kosong, dan file xml. File **HQK Reporting/HQK_Config_Backup.xml** memperlihatkan config service yang sebelumnya telah kita temukan dengan nmap pada port 4386.

    <?xml version="1.0"?>
    <ServiceSettings xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <Port>4386</Port>
      <QueryDirectory>C:\Program Files\HQK\ALL QUERIES</QueryDirectory>
    </ServiceSettings>

![](/images/htb/nest/13.png)

### Abusing HQK Reporting Service
Ada beberapa command yang dapat kita gunakan disana, salah satu command nya adalah DEBUG, debug mode akan memberikan command tambahan untuk network troubleshoting dan masalah konfigurasi, akan tetapi membutuhkan password yang kita sendiri tidak tahu password yang mana. Pada file ‘Debug Mode Password.txt’ tidak berisi apa-apa, kita coba pakai password c.smith pun invalid :(

![](/images/htb/nest/14.png)

Curiga dengan file kosong tadi, kita coba check menggunakan command allinfo pada smbclient, command allinfo akan memberikan seluruh informasi tentang file atau folder, lalu didapatkan informasi bahwa file  ‘Debug Mode Password.txt’ memiliki data stream sebesar 15 bytes, kita coba download data stream tersebut menggunakan perintah get dan didaptkan password debugnya adalah WBQ201953D8w

![](/images/htb/nest/15.png)

![](/images/htb/nest/16.png)

Kita coba menggunakan service port 4386 lagi dan mengaktifkan DEBUG mode, maka available commands akan bertambah command SERVICE, SESSION, SHOWQUERY. Kita menggunakan perintah SETDIR untuk merubah posisi directory, dan perintah SHOWQUERY dengan parameter array output dari perintah LIST untuk melihat isi dari file. 

![](/images/htb/nest/17.png)

Menggunakan SETDIR untuk merubah posisi directory, lalu didapatkan folder LDAP yang berisi file exe dan file config. Disana terdapat credential Administrator yang passwordnya diencrypt seperti password c.smith, lalu kita coba menggunakan script decrypt yang kita pake untuk decrypt password c.smith tadi, namun ternyata gagal :(

![](/images/htb/nest/18.png)

    Domain=nest.local
    Port=389
    BaseOu=OU=WBQ Users,OU=Production,DC=nest,DC=local
    User=Administrator
    Password=yyEq0Uvvhq2uQOcWG8peLoeRQehqip/fKdeG/kjEVb4=


Note :
Pada tahap Previlege Escalation ini, saya tidak dapat melanjutkan karena keterbatasan ilmu yang saya miliki, oleh karena itu tulisan ini saya sajikan hanya sebatas apa yang saya alami saat mengerjakan mesin ini. 

Terimakasih telah membaca, jika dirasa tulisan ini bermanfaat, silahkan Share. Semoga kebermanfaatan ini terus berlanjut!

## References
- [smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html)
- [Read alternate data streams over SMB with Linux](https://superuser.com/questions/1520250/read-alternate-data-streams-over-smb-with-linux)
