---
layout: post
title: "JavaScript'te \"use strict\"; Kullanımı"
abstract: "JavaScript'in EcmaScript 5 ile gelen, artık eski sayılabilecek bir özelliğinden bahsedelim. Bu konuyu Beşiktaş'ta **Fatih Erikli** ile sohbet ederken önemine değindik ve derin bir araştırma sonrası edindiğim bilgileri paylaşmak istedim. Konumuz: `\"use strict\";`."
---

Merhaba,

JavaScript'in EcmaScript 5 ile gelen, artık eski sayılabilecek bir özelliğinden bahsedelim. Bu konuyu Beşiktaş'ta **Fatih Erikli** ile sohbet ederken önemine değindik ve derin bir araştırma sonrası edindiğim bilgileri paylaşmak istedim. Konumuz: `"use strict";`. Kelime anlamı olarak "katı (kuralları) kullan". Bu kodun geçtiği scope'da artık katı kurallar uygulanacak ve kodunuz çalışırken yaptığınız hatalar için uyarılar alacaksınız.

Peki uyulması gereken bu kurallar ne? Bu kural listesine [[1]](http://ecma262-5.com/ELS5_HTML.htm#Annex_C) adresinden erişebilirsiniz.

İlk önemli nokta, strict moda geçmeniz için ya JavaScript kodunuzun ilk satırına`"use strict";` yazacaksınız ya da kod parçanızı fonksiyon kapsamı (context) içerisine alacaksınız.

## 8 Tabanında (Octal) Sayı Tanımlama

    :::javascript
    // kapsam dışı
    (function () {
      // kapsam içi
    })();

Daha önce bir yerlerde kapsamın ne olduğu ile ilgili bilgi vermiştim. Şimdi kapsam içerisine o kısmın strict olması gerektiğini söyleyen kodu ekleyelim.

    :::javascript
    // kapsam dışı
    (function () {
      "use strict";
    })();

Normalde 8 tabanlı (octal) rakam oluşturmak için rakamın başına `0` yazmanız yeterliyken, strict mod'da buna izin verilmez.

    :::javascript
    console.log(012); // 10 çıktısını verecektir.
    console.log( 012 == parseInt(12, 8) ); // true verir.

12'nin 8 tabanında (octal) 10 olduğunu görüyoruz. Aynı değeri başına `0` koyarak da elde edebiliyoruz.

Şimdi bunu strict mod'da kullanalım:

    :::javascript
    // kapsam dışı
    (function () {
      "use strict";
      console.log(012);
    })();

Yepyeni bir hatamız oldu:

    SyntaxError: Octal literals are not allowed in strict mode.

Aynı şekilde

    :::javascript
    // kapsam dışı
    (function () {
      "use strict";

      console.log("\012");

    })();

şeklinde tanımlanan string tabanlı "octal" veri de hata ortaya çıkaracaktır.

Strict mod'un güzel yanı, kodunuzu derli toplu yazıncaya dek bu şekilde hatalar vermesi ve sizi bu kurallara uymaya zorlamasıdır.

## Belirsiz Global Değişken Atamak

`var` keywordünden daha önce çok bahsettik. Strict mod aktif olduğu zaman `var` olmayan değişken tanımlamaları hata olarak karşımıza çıkıyor.

    :::javascript
    (function () {

      "use strict";

      name = "fatih";

    })();

dediğimiz zaman normalde `name` isimli bir değişkenimiz olurdu, artık bunun yerine;

    ReferenceError: name is not defined

şeklinde bir hata ile karşılaşıyoruz.

## Read-Only Verinin Değiştirilmesi

Aynı zamanda yalnızca okunabilir olan (read-only) değişkenlere (`undefined`, `NaN` gibi) atama yapmak strict mod açıkken mümkün olamıyor. Aslında normal modda da yazmanıza izin vermiyor fakat strict mod'da bu exception olarak karşımıza çıkıyor.

Normalde

    :::javascript
    undefined = true

yazabiliyorken, bunu strict moda aldığımızda

    :::javascript
    (function () {

      "use strict";

      undefined = true;

    })();

karşımıza bir hata çıkıyor:

    TypeError: Cannot assign to read only 'undefined' in strict mode


Aynı şekilde, kendi tanımladığımız read-only değişkenlere de dokunulması engelleniyor:

    :::javascript
    (function () {

      "use strict";

      var user = {};

      Object.defineProperty(user, "name", { value: "fka", writable: false });

    })();


Buradaki kodun altına

    :::javascript
    user.name = "fkadev";

dediğim zaman alacağımız hata şuna benziyor olacak

    TypeError: Cannot assign to read only property 'name' of #<Object>

Aynı zamanda yalnızca getter olan bir nesne öğesini de set etmeniz hataya yol açar:

    :::javascript
    (function () {

      "use strict";

      var user = {get name() {return "fka";}};

      user.name = "fkadev"; // Hatalı kod

    })();

Bunun bize vereceği cevap:

    TypeError: Cannot set property name of #<Object> which has only a getter

## Silinemez Verinin Silinmesi

`deletable: true` olan bir nesne öğesi

    :::javascript
    (function () {

      "use strict";

      var user = {};

      Object.defineProperty(user, "name", { value: "fka", deletable: false });

      delete user.name; // Hatalı kod

    })();

Bu durumda alacağımız hata şu şekilde olacak:

    TypeError: Cannot delete property 'name' of #<Object>

## Genişletilemez Nesneler

JavaScript'te ES5 ile gelen güzel özelliklerden birisi de nesneleri dondurabilmeniz. Yani bir nesneye hiçbir şey eklenmemesini sağlayabilirsiniz.

    :::javascript
    Object.preventExtensions(window);

dediğiniz zaman, bu koddan sonraki hiçbir kodda global değişken tanımlanamaz, global fonksiyon tanımlanamaz. Çünkü `window` nesnesini katı hale getirdik.

Bu küçük bilgiden sonra hemen strict mod'da katılaşmış bir veriyi düzenlemeye bakalım.

    :::javascript
    (function () {

      "use strict";

      var user = {};

      Object.preventExtensions(user);

      user.name = "fka"; // Hatalı kod

    })();

Şimdi bu kodun çıktısını görelim:

    TypeError: Can't add property name, object is not extensible

## Tekil Nesne "Key"leri

Nesnelerin "key-value" olduğunu biliyoruz, bu noktada nesneler mantık olarak her zaman bir anahtardan bir adet içermeli.

Normalde JavaScript'te

    :::javascript
    var user = {
      name: "fatih",
      name: "kadir",
      surname: "akin"
    };

dediğimizde, sonradan tanımlanan değer bir öncekini eziyordu, fakat hata yoktu. Şimdi strict mod'da nasıl olduğuna bakalım:

    :::javascript
    (function () {

      "use strict";

      var user = {};

      var user = {
        name: "fatih",
        name: "kadir",
        surname: "akin"
      };

    })();

Yüce strict mod bize çok kızdı:

    SyntaxError: Duplicate data property in object literal not allowed in strict mode

## Mükerrer Fonksiyon Parametreleri

Mükerrer, yani tekrar eden, fonksiyon parametreleri tanımlamak JavaScript'te illegal değil.

    :::javascript
    function topla (x, x, y) {
      console.log(x, x, y);
    }
    topla(1,2,3); // 6 çıkmasını beklerken, cevap 7.

Bu gibi küçük hatalara neden olmamak için "`use strict`;" diyoruz.

    :::javascript
    function topla (x, x, y) {
      "use strict";
      console.log(x, x, y);
    }

Şimdi ise karşımızda bir hata mevcut:

    SyntaxError: Strict mode function may not have duplicate parameter names

## "with" Deyiminin Kullanımı

`with` kullanımından daha önce bahsetmiştik, fakat pek de legal bir yöntem olduğunu söyleyemeyiz. Çünkü kodun okunabilirliği çok fazla düşüyor. Runtime'da neler döndüğünü anlamak güçleşiyor ve hataya çok açık bir yapı sunuyor. Bu sebeplerden ötürü olsa gerek strict mod'da `with` statement'ini kullanamıyorsunuz.

    :::javascript
    function fun() {
      "use strict";
      user = {name: "fka"};
      with (user) {
        alert(name);
      }
    }

Fonksiyonu tanımlamaya çalışırken alacağımız cevap çok zorumuza gitmemeli:

    SyntaxError: Strict mode code may not include a with statement

Yukarıdaki sebeplerden ötürü bu şekilde olması en doğrusu.

## Reserved Kelimelerin Kullanımı

`arguments`, `eval` gibi kelimelerin yanısıra `implements, interface, let, package, private, protected, public, static, yield` gibi kelimelerin kullanımları da gelecek JavaScript versiyonlarında kullanımına açılabilecek olması ihtimaline karşı strict mod'da koruma altında.

    :::javascript
    (function () {
      "use strict";

      var implements = "hello";

    })();

gibi bir *reserved* keyword kullanımı aşağıdaki hatayı ortaya çıkaracaktır:

    SyntaxError: Use of future reserved word in strict mode

---    

Aslına bakarsanız daha fazla strict mod kuralı var, fakat temel olarak bilmeniz gerekenlerin bunlar olduğunu söyleyebilirim. Zaten hatalar sizi kodunuzu düzeltmeye yönlendireceği için bunların tümünü bilmenize gerek yok, kod size öğretecek.

JavaScript yazarken ne kadar çok `"use strict";` kullanırsanız, o kadar temiz kod yazmış olacaksınız. JSHint ve JSLint gibi kod gözden geçiriciler de kodunuzun çok daha derli toplu olması için size yardım edecektir.

Çok daha detaylı bilgi için kaynaklara gözatabilirsiniz.

### Kaynaklar
[1] - http://ecma262-5.com/ELS5_HTML.htm#Annex_C
[2] - https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Functions_and_function_scope/Strict_mode
[3] - http://msdn.microsoft.com/en-us/library/ie/br230269(v=vs.94).aspx
