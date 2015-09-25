---
layout: post
title: Etkili CSS Selector Kullanımı
abstract: CSS’te derinliği olan selectorler verimsizlik yaratıyorlar. Fakat temel seçicimiz (en sağdaki) bir ID selector ise, olaylar daha da kötüye gidiyor.
---

CSS’te derinliği olan selectorler verimsizlik yaratıyorlar. Fakat temel seçicimiz (en sağdaki) bir ID selector ise, olaylar daha da kötüye gidiyor.

Diyelim ki şöyle bir seçicimiz var (ayrıca ID’lerin tekil olması gerektiğini de hatırlayalım):

    :::css
    #nav li ul li a#active {...}

Browserlar sağdan sola okuduklarından beri, işler browserin gözünde şu şekilde yürüyor.

Kontrol ediliyor: `#nav li ul li a#active`

Browser’in cevabı: Evet buldum.

Kontrol edilmeye devam ediliyor: `#nav li ul li a#active`

Browser’in cevabı: Evet zaten buldum ya.

Kontrol edilmeye devam ediliyor: `#nav li ul li a#active`

Browser’in cevabı: Bulduğumu söylememiş miydim?

Kontrol edilmeye hala devam ediliyor, halbuki element bulunmuştu: `#nav li ul li a#active`

Browser’in cevabı: Alooooo, buldum dedik ya!?!.

Hala kontrol ediliyor: `#nav li ul li a#active`

Browser’in cevabı: Dalga geçmesene! Bulduk dedik ya!!! >:(

Hala... ve Nihayet: `#nav li ul li a#active`

Browser’in cevabı: WTF! BULDUM DEDİM YA!!!!!

Fakat çok geç...

Yani CSS’te derinlik vererek select yapacaksanız ve en sağdaki selectorunuz ID ise, direk ID
ile seçmeniz daha fazla performans sağlıyacaktır. Benim sayfamda aynı ID’den birden fazla var
diyorsanız google’ı açın HTML ve CSS dersleri şeklinde arama yaptıktan sonra tekrar bir gözden geçirin, n'olur n'olmaz ;)…

_Not: [www.css-101.org/descendant-selector/go_fetch_yourself.php](http://www.css-101.org/descendant-selector/go_fetch_yourself.php) adresindeki metinden çeviridir._
