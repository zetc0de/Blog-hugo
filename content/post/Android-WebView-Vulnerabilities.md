---
title: "Android WebView Vulnerabilities"
date: 2019-09-11T13:13:23+07:00
draft: false
tags: ["Android"]
categories: [ "Mobile Pentesting" ]
---

## Overview
WebViews digunakan dalam aplikasi android untuk memuat konten dan halaman HTML dalam aplikasi. Tujuan artikel ini adalah untuk memeriksa hal-hal yang akan membuat WebView kurang aman. Baik developer dan penguji penetrasi dapat menggunakan artikel ini sebagai referensi saat melakukan tes penetrasi.

## Loading Clear-Text Content

Jika WebView memuat konten clear-text dari Internet, maka akan mudah untuk dilakukan berbagai bentuk serangan seperti MiTM.

    myWebView.loadUrl("http://www.zetlab.pw/webview");

## SSL Error Handling

Kode di bawah ini menginstruksikan client WebView untuk melanjutkan request ketika terjadi kesalahan SSL. Ini berarti bahwa aplikasi ini rentan terhadap serangan MiTM karena dapat memungkinkan penyerang membaca atau memodifikasi konten yang ditampilkan kepada pengguna karena sertifikat apa pun akan diterima oleh aplikasi.

    @Override
    public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error)
    {
    handler.proceed();
    }

## Javascript Enabled

Mengizinkan konten JavaScript dieksekusi dalam aplikasi melalui WebView mungkin memberi peluang bagi penyerang untuk mengeksekusi kode JavaScript sewenang-wenang untuk melakukan tindakan jahat. Pengaturan ini memungkinkan WebView untuk mengeksekusi kode JavaScript.

    WebSettings webSettings = myWebView.getSettings();
    webSettings.setJavaScriptEnabled(true);

Fungsi JavascriptInterface  menjembatani antara kode JavaScript dan kode Java asli. Ini berarti bahwa kode JavaScript dapat mengakses dan menginject objek Java dan kode Java untuk dipanggil oleh JavaScript kembali. Tergantung pada permission aplikasi, ini dapat memungkinkan penyerang berinteraksi dengan fungsionalitas perangkat (baca SMS, rekaman mikrofon, dll.) 

    setContentView(webView);
    ...
    class JsObject {
    private String sensitiveInformation;
    ...
    public String toString() { return sensitiveInformation; }}
    webView.addJavascriptInterface(new JsObject(), "injectedObject");
    webView.loadData("", "text/html", null);
    webView.loadUrl("http://www.zetlab.pw");

## Accessing Local/Remote Resource

Jika WebView digunakan untuk mengakses konten dari local atau URL, maka penyerang dapat membuat file html berbahaya yang dapat diinject ke dalam aplikasi target melalui **file:scheme** atau menggunakan parameter URL di activity.

    public class MyBrowser extends Activity {
    @override
    public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
     
    WebView webView = (WebView) findViewById(R.id.webview);
     
    // turn on javascript
    WebSettings settings = webView.getSettings();
    settings.setJavaScriptEnabled(true);
     
    String turl = getIntent().getStringExtra("URL");
    webView.loadUrl(turl);
    }
    }

## References
- [Android WebView Vulnerabilities](https://pentestlab.blog/2017/02/12/android-webview-vulnerabilities/)