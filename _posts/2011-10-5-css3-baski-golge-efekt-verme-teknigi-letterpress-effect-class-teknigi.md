---
layout: post
title: "CSS3 ile Yazıya Baskı Gölge Vermek - 2: Class Tekniği"
abstract: Yazılara verilen baskı efekt tekniğini hızlı uygulamak
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Merhaba,

Önceki postumda bu işin detaylarını anlatan bir yazı yazmıştım. Bugün o detayları biraz
daha ilerlettik ve `rgba` kullanarak vermiş olduğumuz tonlamaları CSS’e yaptırdık. Ortaya çıkan sonuç şu oldu:

<iframe src="http://jsfiddle.net/9Dtsj/embedded/css,result"></iframe>

Sadece bu CSS classı ile mükemmel işler yapabiliriz :) Burada gördüğümüz class, `background`’a göre kendisine
gölge ve yeni renk atıyor. Tabii `rgba` kullanarak. Daha önceki yazımda yaptığımızın aynısı aslında, yalnızca
önceki değerleri iptal etmesi için biraz `important` ve biraz da asıl sihri sağlayan `rgba` kattık.

Bu sınıf ile yaptığımız örneklere gelirsek, şu harika işleri görürüz:

<iframe src="http://jsfiddle.net/yhE7D/4/embedded/result,css,html"></iframe>

Harika göründüğünü söylemiştim :) Bu kod içerisine bir de ufak bir button frameworkü kondurdum, belki işinizi görür :)

Bunu harika kullanan tasarımcı arkadaşlar olduğuna eminim, umarım siz de bunu kullanarak yeni işler yaparsınız.

Tek yapmanız gereken yukarıdaki sınıfı bir elemente vermek :) Arkaplanın orta koyu tonlarda olduğu durumlarda daha güzel göründüğünü belirtmekte fayda var.

Bir örnek de arkaplanlı bir değer için verelim, fakat burada sınıf üzerinde `color` değerinde alpha’yı biraz yükselttik,
arkaplandan biraz ayrılması için. Bu tarz ince ayarlar yaparsanız işinizde daha iyi kullanabilirsiniz:

<iframe src="http://jsfiddle.net/fuCbw/embedded/result,css,html"></iframe>

Evet işte şimdi çok orijinal oldu :)

Teşekkürler.
