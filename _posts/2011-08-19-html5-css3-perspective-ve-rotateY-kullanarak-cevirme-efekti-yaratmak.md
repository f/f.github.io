---
layout: post
title: HTML5 + CSS3 "perspective" ve "rotateY" Kullanarak Çevirme Efekti Yaratmak
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

![HTML5-CSS3](http://dvchiddy.com/html5report/images/logo.png)

Merhaba,

Bu aralar HTML5 + CSS3 deneyimlerime daha da hız vermiş bulunmaktayım :) `perspective` ve `rotateY` efektlerini
kullanarak (ve tabii ki transition) basit bir çevirme efekti yaratmak istedim. Kart yukarı kalktığında hafif
bir yükselme yaşıyor ve gölgesi daha dağılıyor. Ve tabii ki burada `backface-visibility`nin en güzel örneğini de görüyoruz.

En güzel örnek olarak bir iskambil kağıdı koymak istedim, umarım beğenirsiniz :)

_Not: -webkit- önekleri yerine -moz- önekleri vererek Firefox’ta çalışmasını sağlayabilirsiniz._

<iframe src="http://jsfiddle.net/fkadev/jSQN3/embedded/result,css,html/"></iframe>
