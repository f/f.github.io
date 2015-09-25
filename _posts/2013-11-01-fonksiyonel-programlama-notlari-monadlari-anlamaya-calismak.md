---
layout: post
title: "Fonksiyonel Programlama Notları 2: Monad'ları Anlamaya Çalışmak"
abstract: Fonksiyonel Programlama maceram devam ediyor, monadları araştırmaya koyuldum.
---

Uzun zamandır **Fonksiyonel programlama** ile başetmeye ve anlamaya çalışıyorum. Bu yolculuk beni matematiğe kadar indirdi ve oldukça da zevkli geçiyor. Kafamı karıştırsa da oldukça zevkli. *(Geçen hafta NTV Yayınlarının **"Sayılar"** isimli kitabını alıp okumaya başladım, matematik o kadar tatlı geldi ki…)*

Bu serüvende karşıma anlaşılması zor olan bambaşka bir yapı çıktı: Monadlar. Bir önceki yazımda bu konuya değinmiştim, fakat bu konuyu biraz daha geliştirme ihtiyacı hissettim.

Konuyla alakalı olarak geçenki yazımdaki kısım tam olarak şu şekildeydi:

> Fonksiyonlar kendi ihtiyacı olan yan etkileri parametre olarak alıp müdahale ediyorlar, böylece yan etki yaratmadan aynı işlemleri yapabiliyoruz. Yani global state'de bir şey tutmadan bir state'i taşıyabiliyoruz.

>     function f(x, y) { return [2*x + 5, y]; }
diye bir fonksiyonumuz olduğunu varsayalım;

>     function g(z, y) { return [z+10, y]; }
diye de başka bir fonksiyonumuz var;

> y'nin taşımamız gereken global bir state olduğunu düşünelim;

>     g.apply(null, f(2, 5)) //=> [19, 5]
Bu durumu biraz irdeleyelim;

>     f(2, 5) //=> [9, 5]
>     g(9, 5) //=> [19, 5]
görüldüğü gibi y değerinin state'ini taşıdık, ve yanetki kullanmadık.

> Bu konuyu anlamamda ssg'nin ekşisozlük'te yazdığı bu entry epey faydalı oldu. Şimdi onun verdiği örneği JavaScript ile yazalım;

> Normalde yan etki kullanarak bir toplama fonksiyonu yazalım;

>   
    var genelToplam = 0;
    function topla(x, y) {
      var toplam = x + y;
      genelToplam += toplam;
      return toplam;
    }
>
    topla(2, 3); //=> 5
    topla(4, 5); //=> 9
    genelToplam; //=> 14
Şimdi bu yanetkiye sahip fonksiyonu yan etkisiz hale getirelim;

>   
    function topla(x, y, genelToplam) {
      var toplam = x + y;
      genelToplam += toplam;
      return [toplam, genelToplam];
    }
>
    function genelToplam() {
      return topla(2, 3, topla(4, 5, 0)[1])[1];
    }
>
    genelToplam() //=> 14

## JavaScript'e Uyarlamak

Üstteki kodun epeyce anlaşılmaz olduğu kesin, *zaten yazarken içime sinmemişti*.

Şimdi bunu biraz daha JavaScript gibi düşünelim:

Bir toplama işlemi fonksiyonu yapsaydık:

{% highlight js %}
function Toplam(sayi, genel) {
  genel += sayi;
  return genel;
}

var toplam = Toplam(10, 2); //=> 12
{% endhighlight %}

şeklinde bir değer elde edecektik. Daha sonra yeni bir işlem için;

{% highlight js %}
var genelToplam = Toplam(10, 0); //=> 10
genelToplam = Toplam(20, genelToplam); //=> 30
genelToplam = Toplam(12, genelToplam); //=> 42
{% endhighlight %}

şeklinde bir yapı sağlayacaktık. Sürekli bir state tutuyoruz ve bu state üzerinden yürüyoruz. Şimdi de daha fonksiyonel şekilde yazmaya çalışalım:

{% highlight js %}
var genelToplam = Toplam(10, Toplam(20, Toplam(12, 0))); //=> 42
{% endhighlight %}

Bu da doğru, fakat hala yetersiz ve state kullanmak istemiyorsak pek de kullanılabilir değil. Şimdi de bu fonksiyonu daha "monad" gibi ve fonksiyonel olarak implemente etmeye çalışalım.

Hatırlayacaksınız ki fonksiyonel programlamada işler hep küçük, saf fonksiyonlar halinde uygulanıyor.

{% highlight js %}
function Toplam(sayi, genel) {

  if (isNaN(genel)) { genel = 0; }

  genel += sayi;

  // Yanetki yok, state değiştirilmiyor
  function ekle(sayi) {
    return Toplam(sayi, genel);
  }

  // Yanetki yok
  function toplam() {
    return genel;
  }

  return { ekle: ekle, toplam: toplam };
}
{% endhighlight %}

Hiç yanetki oluşturmadan state kullanmamız gerektiren bir durumu kullanarak değeri taşıdık.

{% highlight js %}
Toplam(10).ekle(20).ekle(12).toplam() //=> 42
{% endhighlight %}

Şimdi de bunu biraz daha JavaScript gibi yapalım, JavaScript'te eğer bir nesne değeri alınmaya zorlanıyorsa `valueOf` methodu çağırılır.

{% highlight js %}
..
return { ekle: ekle, toplam: toplam, valueOf: toplam };
..
{% endhighlight %}

diyerek daha kullanılabilir bir hale getirebiliriz.

{% highlight js %}
console.log("30 ile 12'nin toplamı " + Toplam(30).ekle(12) + " eder.");
// => 30 ile 12'nin toplamı 42 eder.
{% endhighlight %}

veya

{% highlight js %}
Toplam(15).ekle(6) * 2 //=> 42
{% endhighlight %}

şeklinde kullanabiliriz.

## Monad Kanunları

Haskell'de Monad kanunları mevcutmuş, bu kanunlara uymayanlar monaddan sayılmıyormuş; madem öyle; inceleyelim:

{% highlight haskell %}
-- Left
return a >>= f                  = f a
-- Right
m        >>= return             = m
-- Assoc
m        >>= (\x -> f x >>= g)  = (m >>= f) >>= g
{% endhighlight %}

Yani diyor ki;

{% highlight js %}
// Left
Monad(a).bind(f) == Monad(f(a))
// Right
Monad(a).bind(function (x) {return x}) == Monad(a)
// Assoc
Monad(a).bind(f).bind(g) == Monad(a).bind(function (x) {return g(f(x))})
{% endhighlight %}

Yani Matematiksel olarak;

{% highlight js %}
f(a) == f(a)
(function (x) { return x; })(a) == a
g o f(a) = g o f(a)
{% endhighlight %}

kurallarını sağlamalı…

Bu kuralları sağlamak içinse şunlar gerekli:

 - İki method implementasyonu olmalı: `bind` ve `return`
 - Üç kurala uymalı: **left identity**, **right identity**, **associativity**

Şimdi bu Monad'ımızı bu şartları sağlayarak tekrar implemente edelim:

`bind` methodunu chaining yapmak için implementasyonumuza ekleyelim:

{% highlight js %}
..      
  function bind(fn) {
    return Toplam(fn.call(this, sayi));
  }
..
{% endhighlight %}

`toplam` fonksiyonumuz zaten return değerini sağlıyor.

### Left Identity:

Tüm x ve fn'ler için:

{% highlight js %}
Toplam(x).bind(fn).toplam() == Toplam(fn(x)).toplam() //=> true
{% endhighlight %}

### Right Identity:

Tüm x'ler için:

{% highlight js %}
Toplam(10).bind(function (x) {return x;}).toplam() == Toplam(10).toplam() //=> true
{% endhighlight %}

### Associativity

Tüm x, fn ve gn'ler için

{% highlight js %}
Toplam(10).bind(fn).bind(gn).toplam() == Toplam(10).bind(function (x) {return gn(fn(x));}).toplam() //=> true
{% endhighlight %}

Bu kuralları da kitabına uydurduğumuza göre, artık legal bir monad'a sahibiz demektir. :)

{% highlight js %}
function Toplam(sayi, genel) {

  if (isNaN(genel)) { genel = 0; }

  genel += sayi;

  // Yanetki yok, state değiştirilmiyor
  function ekle(sayi) {
    return Toplam(sayi, genel);
  }

  // Yanetki yok
  function toplam() {
    return genel;
  }

  function bind(fn) {
    return Toplam(fn.call(this, sayi));
  }

  return { ekle: ekle, toplam: toplam };
}
{% endhighlight %}

#### Kaynaklar

*Konuyla alakalı okuduğum kaynakları hemen okuduktan sonra Twitter adresimde paylaşıyorum, oradan takip edebilirsiniz. [http://twitter.com/fkadev](http://twitter.com/fkadev)*
