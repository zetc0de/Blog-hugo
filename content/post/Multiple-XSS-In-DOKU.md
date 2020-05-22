---
title: "[Bug Bounty] Multiple Reflected XSS in DOKU Staging"
date: 2019-06-10T12:34:48+07:00
draft: false
tags: [ "Bug Bounty", "XSS"]
---

## About DOKU
DOKU Wallet adalah sebuah layanan dompet elektronik (Wallet) yang dikeluarkan oleh PT. Nusa Satu Inti Artha yang dilengkapi dengan fitur penghubung ke kartu kredit dan uang elektronik/cash wallet. DOKU adalah penyedia layanan pembayaran elektronik dan manajemen resiko pertama di Indonesia.

## Reflected XSS
Reflected XSS adalah dimana script yang diinjeksi akan ditampilkan pada web, melalui pesan error, hasil pencarian, atau dari request input (GET/POST). Reflected XSS juga biasa disebut dengan Non-Persistent XSS.


## Reflected XSS Pada DOKU (staging.doku.com)
### BNI Billpayment Simulator 
{{< youtube 3KObPj8tcms >}}
### BNI Simulator 
{{< youtube 4t2yg0oMprM >}}
### BRI Simulator
{{< youtube TNcdfGC3mlw >}}
### CIMB Simulator  
{{< youtube AJ5ji_jTFCw >}}
### Danamon Simulator 
{{< youtube B7kfDo9Zkts >}}
### Mandiri Simulator 
{{< youtube ffrLP4sXyl8 >}}
### BCA Simulator 
{{< youtube 0VnXw_Bjv-8 >}}
### Sinarmas Simulator 
{{< youtube AlavYUregNI >}}

## Reference
- [Cross Site Scripting (XSS)
](https://owasp.org/www-community/attacks/xss/)