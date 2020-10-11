---
title: "Powershell Download File One Liners"
date: 2020-10-11T14:26:00+07:00
tags: [ "Windows", "Powershell"]
draft: false
---


## PowerShell (any version)
    (New-Object System.Net.WebClient).DownloadFile("https://example.com/archive.zip", "C:\Windows\Temp\archive.zip")  

## PowerShell 4.0 & 5.0
    Invoke-WebRequest "https://example.com/archive.zip" -OutFile "C:\Windows\Temp\archive.zip"
