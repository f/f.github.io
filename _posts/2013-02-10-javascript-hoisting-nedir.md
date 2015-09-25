---
layout: post
title: JavaScript Hoisting
abstract: Hoisting, yani JavaScript'te "yukarıya taşıma" durumunu irdeleyelim.
---

Bu kitap basıma girdikten sonraki ilk yazım. Kitapta bu konudan bahsetmedim, belki sonraki baskılarda ekleme fırsatım olur.

JavaScript ilginç bir dil. Syntax yorumlanırken arkada bilmediğimiz, anlamadığımız, farketmediğimiz bir çok şey oluyor. Hoisting konusu da bunlardan birisi.

JavaScript'te scope bazlı hoisting yani yukarı kaldırma durumu sözkonusudur. Peki bu ne demek? Hemen kolayca başlayalım ve irdeleyelim:

Normal şartlar altında

    :::javascript
    console.log(x);

değeri bize **"ReferenceError: x is not defined"** diye bir hata verir. Biz de bunun için doğal bir refleks olarak:

    :::javascript
    var x;
    console.log(x);

yazarız. Böylece ilk önce x'i tanımlar, sonra console'a veririz.

İşleri biraz değiştirelim.

    :::javascript
    console.log(x);
    var x;

yazarsak ne olacak? Aklımıza gelen ilk cevap şu: "Hata verir". Çünkü bizce JavaScript var olmayan bir değişkeni önce basıp sonra tanımladı. -- yanıldık.

JavaScript için bu kod şu şekilde yorumlandı:

    :::javascript
    var x;
    console.log(x);

`x` değişkeni *yukarı kaldırıldı* ve bu sorun giderildi.

## Neler Oluyor?

JavaScript `identifier` verileri parse-time yani parse ederken düzenliyor. Bu sırada hoisting yapıyor.

Şimdi scope içerisinde bir örnek yapalım:

    :::javascript
    var x = 10;
    // Yeni bir scope yaratalım
    console.log(x);
    (function() {
      console.log(x);
      var x = 11;
      console.log(x);
    })();

Bu kodun çıktısının (kodu gözümüzle takip ettiğimizde):

    :::javascript
    10
    10
    11

olmasını beklerken; çıktının

    :::javascript
    10
    undefined
    11

olduğunu görüyoruz.

Şimdi bunu hoisting ile açıklayalım; bu kod aslında hiç bir zaman bu şekilde yorumlanmadı. Parse-time'da kod şu şekilde algılandı:

    :::javascript
    var x;
    x = 10;
    console.log(x);
    (function() {
      var x;
      console.log(x);
      x = 11;
      console.log(x);
    })();

Şimdi bu kodu gözümüzle takip ettiğimizde

    :::javascript
    10
    undefined
    11

sonucunu vermesini anormal karşılamayacağız. Çünkü `var x;` scope içerisinde yukarı çekilerek `x` değişkenini local hale getiriyor.

    :::javascript
    var x = 10;
    // Yeni bir scope yaratalım
    console.log(x);
    (function() {
      console.log(x);
      x = 11; // var keywordünü kaldırdık.
      console.log(x);
    })();

şeklinde yazsaydık, bu kez ilk beklediğimiz çıktıyı elde edecektik.

Biraz önceki örneği şimdi de bir fonksiyon için uygulayalım:

    :::javascript
    console.log(x);
    var x = function() {};

bu kod `undefined` çıktısı verecektir. Fakat hata vermez. Çünkü aslında

    :::javascript
    var x;
    console.log(x);
    x = function() {};

şeklinde yorumlandı. Yani burada fonksiyon değil, `var` keyword'ü yukarı çekildi. **Bu noktada kafanın karışmaması önemli.** Bu yüzden şimdi fonksiyonların yukarı çekilmesinden bahsedelim:

## Fonksiyon Hoisting

`var` keywordünün yukarı kaldırılması durumu aynı zamanda fonksiyonlar için de geçerli. Burada da çok ince bir çizgi olan

    :::javascript
    var oley = function() {}

ile

    :::javascript
    function oley() {}

arasındaki fark ortaya çıkıyor.

    :::javascript
    console.log(x);
    var x = function() {};

örneğini incelemiştik. Burada `x` değeri `undefined` olarak beliriyordu.

    :::javascript
    console.log(x);
    function x() {};

dediğimizde ise çıktının

    :::javascript
    function x() {};

olduğunu göreceğiz.

Dolayısıyla;

    :::javascript
    function x() {};

JavaScript parse edilirken yukarı çekildi;

    :::javascript
    var x = function() {};

bir fonksiyon deklarasyonu olarak değil, *değişken deklarasyonu* olarak yukarı çekildi. Yani değerinin ne olduğuna `runtime` sırasında karar verilecek.

İlk kodumuz;

    :::javascript
    function x() {};
    console.log(x);

şeklinde yukarı çekilirken, diğer kodumuz;

    :::javascript
    var x;
    console.log(x);
    x = function() {};

şeklinde yukarı çekildi.

Gördüğünüz gibi, JavaScript yer yer farklı davranışlar sergileyebiliyor. Bu davranışları dikkate alarak davranmak ve kodumuzu buna göre yazmak gerekiyor.

## Sonuç

Görüldüğü gibi, burada

    :::javascript
    console.log(x);
    x = 11;

ile

    :::javascript
    console.log(x);
    var x = 11;

arasında ve aynı zamanda;

    :::javascript
    var HelloWorld = function () {}

ile

    :::javascript
    function HelloWorld() {}

arasında oldukça fark var. :)
