---
layout: post
title: "\"Vaklıyorum, öyleyse ördeğim\""
abstract: Duck Typing hakkında kısa bilgi.
---

Merhaba, son zamanlar gördüğüm bu terimi biraz açıklamak isterim, eğer bilmiyorsanız:)

Duck Typing, sürekli kullandığımız fakat ismini bilmediğimiz bir yöntemdir.

## Vaklıyorsa, ördektir

En çok Javascript ile birlikte görmüş olduğumuz bu teknik, şu manaya gelir **"vaklıyorsa ördektir"** :)

Şu kullanıma bakın:

    :::javascript
    function Ordek() {}

    Ordek.prototype = {

	    vak : function()
	    {
		    alert('vak vak');
	    },
	    yuz : function()
	    {
		    alert('yüz..');
	    }
    };

    var ordek = new Ordek();

    if (ordek.vak) // Vaklıyor mu?
	    ordek.yuz(); // O zaman ördek.

Şeklinde açıklayabiliriz :) Güncel programlamadaki kullanımı ise şu şekildedir:

    :::javascript
    if (data.join)
	    alert('Join yapabildiğine göre bu bir array olabilir.');

İşte bu tekniğe Duck typing deniyor, yani her zaman kullandığımız bir olay :) Sağlıcakla...

_Güncelleme: 12 Kasım 2012. Başlık [Burak Yiğit Kaya](http://twitter.com/madbyk) tarafından önerilmiştir._
