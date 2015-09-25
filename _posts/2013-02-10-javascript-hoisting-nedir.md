---
layout: post
title: JavaScript Hoisting
abstract: Hoisting, yani JavaScript'te "yukarıya taşıma" durumunu irdeleyelim.
---

Bu kitap basıma girdikten sonraki ilk yazım. Kitapta bu konudan bahsetmedim, belki sonraki baskılarda ekleme fırsatım olur.

JavaScript ilginç bir dil. Syntax yorumlanırken arkada bilmediğimiz, anlamadığımız, farketmediğimiz bir çok şey oluyor. Hoisting konusu da bunlardan birisi.

JavaScript'te scope bazlı hoisting yani yukarı kaldırma durumu sözkonusudur. Peki bu ne demek? Hemen kolayca başlayalım ve irdeleyelim:

Normal şartlar altında

{% highlight js %}
console.log(x);
{% endhighlight %}

değeri bize **"ReferenceError: x is not defined"** diye bir hata verir. Biz de bunun için doğal bir refleks olarak:

{% highlight js %}
var x;
console.log(x);
{% endhighlight %}

yazarız. Böylece ilk önce x'i tanımlar, sonra console'a veririz.

İşleri biraz değiştirelim.

{% highlight js %}
console.log(x);
var x;
{% endhighlight %}

yazarsak ne olacak? Aklımıza gelen ilk cevap şu: "Hata verir". Çünkü bizce JavaScript var olmayan bir değişkeni önce basıp sonra tanımladı. -- yanıldık.

JavaScript için bu kod şu şekilde yorumlandı:

{% highlight js %}
var x;
console.log(x);
{% endhighlight %}

`x` değişkeni *yukarı kaldırıldı* ve bu sorun giderildi.

## Neler Oluyor?

JavaScript `identifier` verileri parse-time yani parse ederken düzenliyor. Bu sırada hoisting yapıyor.

Şimdi scope içerisinde bir örnek yapalım:

{% highlight js %}
var x = 10;
// Yeni bir scope yaratalım
console.log(x);
(function() {
  console.log(x);
  var x = 11;
  console.log(x);
})();
{% endhighlight %}

Bu kodun çıktısının (kodu gözümüzle takip ettiğimizde):

{% highlight js %}
10
10
11
{% endhighlight %}

olmasını beklerken; çıktının

{% highlight js %}
10
undefined
11
{% endhighlight %}

olduğunu görüyoruz.

Şimdi bunu hoisting ile açıklayalım; bu kod aslında hiç bir zaman bu şekilde yorumlanmadı. Parse-time'da kod şu şekilde algılandı:

{% highlight js %}
var x;
x = 10;
console.log(x);
(function() {
  var x;
  console.log(x);
  x = 11;
  console.log(x);
})();
{% endhighlight %}

Şimdi bu kodu gözümüzle takip ettiğimizde

{% highlight js %}
10
undefined
11
{% endhighlight %}

sonucunu vermesini anormal karşılamayacağız. Çünkü `var x;` scope içerisinde yukarı çekilerek `x` değişkenini local hale getiriyor.

{% highlight js %}
var x = 10;
// Yeni bir scope yaratalım
console.log(x);
(function() {
  console.log(x);
  x = 11; // var keywordünü kaldırdık.
  console.log(x);
})();
{% endhighlight %}

şeklinde yazsaydık, bu kez ilk beklediğimiz çıktıyı elde edecektik.

Biraz önceki örneği şimdi de bir fonksiyon için uygulayalım:

{% highlight js %}
console.log(x);
var x = function() {};
{% endhighlight %}

bu kod `undefined` çıktısı verecektir. Fakat hata vermez. Çünkü aslında

{% highlight js %}
var x;
console.log(x);
x = function() {};
{% endhighlight %}

şeklinde yorumlandı. Yani burada fonksiyon değil, `var` keyword'ü yukarı çekildi. **Bu noktada kafanın karışmaması önemli.** Bu yüzden şimdi fonksiyonların yukarı çekilmesinden bahsedelim:

## Fonksiyon Hoisting

`var` keywordünün yukarı kaldırılması durumu aynı zamanda fonksiyonlar için de geçerli. Burada da çok ince bir çizgi olan

{% highlight js %}
var oley = function() {}
{% endhighlight %}

ile

{% highlight js %}
function oley() {}
{% endhighlight %}

arasındaki fark ortaya çıkıyor.

{% highlight js %}
console.log(x);
var x = function() {};
{% endhighlight %}

örneğini incelemiştik. Burada `x` değeri `undefined` olarak beliriyordu.

{% highlight js %}
console.log(x);
function x() {};
{% endhighlight %}

dediğimizde ise çıktının

{% highlight js %}
function x() {};
{% endhighlight %}

olduğunu göreceğiz.

Dolayısıyla;

{% highlight js %}
function x() {};
{% endhighlight %}

JavaScript parse edilirken yukarı çekildi;

{% highlight js %}
var x = function() {};
{% endhighlight %}

bir fonksiyon deklarasyonu olarak değil, *değişken deklarasyonu* olarak yukarı çekildi. Yani değerinin ne olduğuna `runtime` sırasında karar verilecek.

İlk kodumuz;

{% highlight js %}
function x() {};
console.log(x);
{% endhighlight %}

şeklinde yukarı çekilirken, diğer kodumuz;

{% highlight js %}
var x;
console.log(x);
x = function() {};
{% endhighlight %}

şeklinde yukarı çekildi.

Gördüğünüz gibi, JavaScript yer yer farklı davranışlar sergileyebiliyor. Bu davranışları dikkate alarak davranmak ve kodumuzu buna göre yazmak gerekiyor.

## Sonuç

Görüldüğü gibi, burada

{% highlight js %}
console.log(x);
x = 11;
{% endhighlight %}

ile

{% highlight js %}
console.log(x);
var x = 11;
{% endhighlight %}

arasında ve aynı zamanda;

{% highlight js %}
var HelloWorld = function () {}
{% endhighlight %}

ile

{% highlight js %}
function HelloWorld() {}
{% endhighlight %}

arasında oldukça fark var. :)
