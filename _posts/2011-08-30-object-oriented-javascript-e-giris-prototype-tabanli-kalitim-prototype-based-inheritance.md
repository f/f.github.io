---
layout: post
title: "Object Oriented Javascript'e Giriş: Prototype Tabanlı Kalıtım (Prototype-Based Inheritance)"
abstract: "Nesne Yönelimli JavaScript'e giriş Dersleri, 3. Bölüm: Prototype Tabanlı Kalıtım"
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Merhaba,

Nesnel Javascript’e Giriş dizisinin bundan bir önceki başlığında prototiplere temel olarak değinmiş ve örnek bir
sınıf yazmıştık. Prototiplerin bir sınıfın nesneye gönderdiği değerleri olduğunu görmüştük.

İnternet üzerinde bulduğum bir şema üzerinden gitmek istiyorum.

![Prototype](http://f.cl.ly/items/1c0w3r3V1C1A1q3d373P/Prototype.gif)

Aslında bütün olay bu. Şimdi bu şemanın bir de kodunu yazalım ki anlatması daha kolay olsun.

<iframe src="http://jsfiddle.net/fkadev/LxQMQ/embedded/js,result"></iframe>

Burada `A` fonksiyonu ile `B` fonksiyonu arasında organik bir bağ yok. Fakat şemada gördüğümüz gibi `X`’nin `__proto__`
su `B`’nin `prototype`‘ına eşit. Demek ki bunları kalıtmak için bu şekilde bir yapıya eriştirmemiz gerekiyor.

Şimdi mantık çerçevesinde düşünelim, bizim bir `Animal` nesnemiz olsun. Bu `Animal` nesnesinin prototipinde nesne bazlı
değişkenler olsun, her nesneye göre değişebilen değerler. Her nesne yarattığımızda bu değerler nesnenin kendisine has
değerler olsun. Fakat biz yeni bir sınıf oluşturmak istiyoruz ve bu da `Animal` sınıfından türemiş olmalı. `Bird` adlı bir
sınıfımız olmalı ve bu da `Animal` sınıfının prototipine sahip olmalı ki, yeni türeyen nesneler `Bird`’in aynı zamanda da
`Animal`’ın prototipine sahip olsun. Fakat `constructor` `Animal`’ın değil, `Bird`’in kendisi olmalı.

Son cümleyi tekrar yazıyorum: **`Bird` adlı bir sınıfımız olmalı ve bu da `Animal` sınıfının prototipine sahip olmalı ki,
yeni türeyen nesneler `Bird`’in aynı zamanda da `Animal`’ın prototipine sahip olsun. İşte burda tüm mantığı anlattık zaten.
İki prototipi birleştirmemiz gerekiyor. Peki bunu nasıl yapabiliriz?

Yukarıdaki şemaya göre baktığımızda, `B`’nin `A`’nın prototipini kalıtıyor olması gerektiğini görürüz. Yani
`B.prototype = A.prototype` dersek olabilir gibi görünüyor... Hemen bakalım:

<iframe src="http://jsfiddle.net/fkadev/jTRSJ/embedded/js,result"></iframe>

Evet gördüğümüz gibi oldu. Fakat bir sorun var burada. Eğer biz `B.prototype`’ı değil de `A.prototype`’ı değştirmek istersek,
ondan kalıtılan `A` da etkilenecek. Hemen örnek verelim:

<iframe src="http://jsfiddle.net/fkadev/jTRSJ/1/embedded/js,result"></iframe>

Demek ki, `B.prototype = A.prototype` yaptığımızda klonlama değil, referans gösterme işlemi yapmış oluyormuşuz. Fakat bizim
işimize yarayan, klonlamak. JavaScript’te klonlama işini ise `Object.create` methodu yapar.

<iframe src="http://jsfiddle.net/fkadev/jTRSJ/3/embedded/js,result"></iframe>

Sorun var. Her iki sınıf da kendi `constructor`‘unu en tepedeki `constructor` sanıyor. O halde bunu düzenlememiz lazım.
Fakat bu sorun neden kaynaklandı buna değinelim. `B.prototype`’a `A.prototype`’dan bir kopya atadığımızda, aslında `constructor`’u
da atıyoruz, çünkü `A.prototype.constructor`, fonksiyonun kendisini veriyor. O halde kopyalamadan sonra, bozulan `constructor`u
geri almalıyız.

<iframe src="http://jsfiddle.net/fkadev/jTRSJ/6/embedded/js,result"></iframe>

Evet kalıtımlı bir sınıf oluşturduk. Şimdi tek bir sorunumuz kaldı, B’nin prototipi, kalıtıldığı sınıfa nasıl ulaşacak? Yani parent’a.

`__proto__` nesnesi, nesnenin asıl prototipini kendisine söyler. `B` sınıfının asıl prototipi `A` olduğu için `__proto__` üzerinden
bir üst sınıfa erişim sağlayabiliriz.

<iframe src="http://jsfiddle.net/fkadev/jTRSJ/8/embedded/js,result"></iframe>

Demek ki, `this.__proto__` kendi kendisinin prototipini verirken, `this.__proto__.__proto__` prototipinin asıl prototipini yani üst sınıfı verecek.
Yani diğer dillerdeki `parent`, `super` mantığı, JavaScript’te `this.__proto__.__proto__` :) Biraz uzun fakat kısaltmak imkansız değil.

{% highlight js %}
Object.prototype.super = function() {
  return this.__proto__.__proto__;
}
{% endhighlight %}

diyerek `this.super().constructor` şeklinde bir erişim sağlayabilirsiniz.

<iframe src="http://jsfiddle.net/fkadev/jTRSJ/9/embedded/js,result"></iframe>

Burada aynı zamanda, Object nesnesine de bir method eklemiş ve bir örnek yaratmış olduk :)

Bir dahaki konuda sınıf oluşturmak için kullandığımız bu yöntemlerin nasıl daha kısaltabileceğimiz konusuna değineceğiz :)
Ayrıca `__defineGetter__` ve `__defineSetter__` özelliklerine de değineceğiz. Son değindiğimiz `super()` konusunu biraz daha kısaltabiliriz :)

Teşekkürler.
