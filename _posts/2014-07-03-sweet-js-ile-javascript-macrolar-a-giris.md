---
layout: post
title: Sweet.js ile JavaScript Macrolar'a Giriş
abstract: Sweet.js ile JavaScript Macrolar kullanarak kodunuzu modüler hale getirin. Bu yazıda çok ufak bir giriş yapacağız :)
---

Merhabalar,

Bildiğiniz gibi, JavaScript'te bir çok şeyi tekrar ederek aslında aynı kodu sürekli yazıyoruz ve bu çoğu zaman büyük zaman kaybı ve kod karmaşası olarak dönüyor.

{% highlight js %}
for (var i = 0; i < 10; i++) {
    console.log(i);
}
{% endhighlight %}

Bu kod üzerinde tekrar ettiğimiz ve aslında *daha kolay* olabilecek bir çok şey var.

{% highlight js %}
from 0 to 10 as i {
    console.log(i);
}
{% endhighlight %}

Biraz daha *deklaratif* ve daha okunabilir/düzenlenebilir bir kod.

Hatta biraz daha kısaltarak,

{% highlight js %}
to 10 as i {
  console.log(i);
}
{% endhighlight %}

desek hiç de fena olmazdı. Bunun için kullanacağımız araç, Mozilla tarafından geliştirilen [Sweet.js][1].

Sweet.js Macromuzu [macro editor][2] kullanarak düzenleyebiliriz.

Şimdi yukarıda öngördüğümüz makroyu yazalım, ama öncesinde bir makroyu nasıl yazdığımıza bakalım:

{% highlight js %}
macro <isim> {
    rule { <kod tasarımı> } => { <çıktı> }
}
{% endhighlight %}

şeklinde bir yapı ile makromuzu tasarlıyoruz. Ve her değişkenimizin başına bir `$` işareti koyuyoruz.

Çağırmak istediğimiz zaman ise

{% highlight html %}
<isim> <kod tasarımı>
{% endhighlight %}

diyerek makromuzu çağırıyoruz.

{% highlight js %}
macro degisken {
    rule { $x } => { var $x; }
}
{% endhighlight %}

Bu makroyu şu şekilde kullandığımızda:

{% highlight js %}
degisken x = 10
{% endhighlight %}

aşağıdaki sonucu elde edeceğiz:

{% highlight js %}
var x = 10;
{% endhighlight %}

## Makro Tasarımı

Tabii bu çok basit bir örnek, şimdi yukarıda kurguladığımız `for` makrosunu deneyelim:

{% highlight js %}
to 10 as index {
    console.log(index);
}
{% endhighlight %}

Yukarıdaki örneğin aşağıdaki tasarım şablonuna denk geldiğini hissedebilirsiniz:

{% highlight js %}
to $value as $index $code
{% endhighlight %}

Bu noktada `$value` değeri `10`, `$index` olarak `index` ve `$code` olarak `{ console.log(index); }` vermiş oluyoruz. O halde hemen makromuzu yazalım:

{% highlight js %}
macro to {
    rule { $value as $index $code } => {
        ...
    }
}
{% endhighlight %}

`...` yazdığımız yere, bu makronun tam olarak neyi karşıladığını yazalım;

{% highlight js %}
macro to {
    rule { $value as $index $code } => {
        for (var $index = 0; $index < $value; $index++) $code
    }
}
{% endhighlight %}

Artık bir makroya sahibiz:

{% highlight js %}
to 30 as i {
    console.log(i);
}
{% endhighlight %}

dediğimizde

{% highlight js %}
for (var i = 0; i < 30; i++) {
    console.log(i);
}
{% endhighlight %}

şeklinde bir çıktı sağlamış olacağız.

Şimdi bu makroyu biraz daha genişletelim ve yeni bir tasarım daha ekleyelim,

Bu kez tasarımda başlangıç değerini de verelim. Yani

{% highlight js %}
to 30 start from 10 as i {
    console.log(i);
}
{% endhighlight %}

yazdığımızda döngü `10`dan başlasın:

{% highlight js %}
macro to {
    rule { $value as $index $code } => {
        for (var $index = 0; $index < $value; $index++) $code
    }

    rule { $value start from $from as $index $code } => {
        for (var $index = $from; $index < $value; $index++) $code
    }
}
{% endhighlight %}

Şimdi `to` makrosu iki farklı tasarıma farklı şekilde cevap verebiliyor hale geldi.

{% highlight js %}
to 30 start from 10 as i {
    console.log(i);
}
{% endhighlight %}

dediğimizde

{% highlight js %}
for (var i = 10; i < 30; i++) {
    console.log(i);
}
{% endhighlight %}

çıktısı alacağız.

## Makroların Birlikte Kullanımı

Sweet.js'in güzel bir tarafı da şu ki, **bir makro diğer bir makroyu kullanabiliyor.**

Şimdi hemen bunu yukarıdaki örneği geliştirerek yapalım:

`to 30 start from 10 as i` gibi bir şablon daha da güzel hale getirilebilir:

{% highlight js %}
from 10 to 30 as index
{% endhighlight %}

dersek, daha okunabilir bir hale gelecek gibi görünüyor. O halde bir `from` makrosu yazmamız gerekecek.

{% highlight js %}
macro from {
    rule { $from to $value as $index $code } => {}
}
{% endhighlight %}

Bu makroyla yukarıdaki şablonu tanımlamış olduk, şimdi bu makro içerisinden `to` makrosunu uyandıralım:

{% highlight js %}
macro from {
    rule { $from to $value as $index $code } => {
        to $value start from $from as $index $code
    }
}
{% endhighlight %}

dediğimizde, `from` makrosu, `to` makrosunu da çağırmış olacak.

Bütün olarak aşağıdaki kodu deneyelim:

{% highlight js %}
macro from {
    rule {
        $start to $to as $index $block
    } => {
        to $to start from $start as $index $block
    }
}

macro to {
    rule {
        $to start from $from as $index $block
    } => {
        for (var $index = $from; $index < $to; $index++) $block
    }
    rule {
        $to as $index $block
    } => {
        for (var $index = 0; $index < $to; $index++) $block
    }
}


to 30 as i {
    console.log(i);
}

from 10 to 30 as index {
    console.log(index);
}

to 10 start from 2 as i {
    console.log(i);
}
{% endhighlight %}

Bu şekilde yeni bir döngü sağlayan makro oluşturmuş olduk.

[Sweet.js][1] çok daha detaylı bir makro kütüphanesi ve bu anlamda detaylıca incelemeniz çok faydalı olacaktır. Makro kullanımlarında **tekrarlar, şablonlar, 'hijyen', `infix`** gibi konuları detaylıca incelemek için [http://sweetjs.org/doc/main/sweet.html][3] adresine göz atmanız son derece faydalı olur :)

Vaktim olursa ikinci kısmını da yazmayı planlıyorum :)

[1]: http://sweetjs.org
[2]: http://sweetjs.org/browser/editor.html
[3]: http://sweetjs.org/doc/main/sweet.html
