---
layout: post
title: Fonksiyonel Programlama Notları ve JavaScript
abstract: Son dönemlerde fonksiyonel programlamayı öğrenmek için epeyce bir okuma yaptım, bu yazıda da okuduklarımdan edindiğim notları paylaşıyorum.
---

*Bu yazıda yazdıklarım araştırmalardan edindiğim notları içeriyor.*

## Fonksiyonel Programlama

Fonksiyonel programlama işin daha fazla matematiğe kaydığı bir programlama türü. OOP'de ne kadar nesne varsa, FP'de o kadar matematik var. "Fonksiyonel programlamadaki fonksiyonlar, aynı matematikteki fonksiyonlar gibi:

    f(x) = 2*x + 5

dediğimizde bunun her zaman `return` eden bir fonksiyon olduğunu biliriz.

    :::javascript
    function f(x) { return 2*x + 5; }

Bu işlemin JavaScript versiyonu.

Fonksiyonel programlamanın en büyük özelliklerinden birisi de diğer bir çok imparative dile göre, fonskiyonların **prosedür**den daha çok bir veri türü olması. Bu fonksiyonlara farklı bir bakış açısı getiriyor.

*Mutable ve Immutable veri türleriyle ilgili yazıya [buradan ulaşabilirsiniz](/javascriptte-mutable-ve-immutable-veri-turleri/)*

### Saf Fonksiyonlar ve Yan Etki

"Global değişken" konusu biraz FP'nin mantığına aykırı. Bu anlamda OOP ile aralarında epey farklılık olduğu aşikar. Eğer bir fonksiyon kendisi kapsamındaki dışındaki bir değeri değiştiriyorsa veya etkiliyorsa buna **yan etki** (side-effect) deniyor.

Mesela bir fonksiyonun bir dosyaya yazması veya konsola çıktı vermesi yan etki sayılabilir.

    :::javascript
    function f(x) { y = 5; return 2*x + 5; }

Mesela bu yan etkiye sahip bir fonksiyon. Çünkü `y` değişkeni fonksiyonun bir parametresi veya sahip olduğu bir değişken değil.

**void fonksiyon** kavramı, FP'de tamamen yan etki. Çünkü bir fonksiyon bir şey döndürmüyorsa kesinlikle yan etki yapıyordur, veya işe yaramıyordur.

    :::javascript
    function f(x) { 2*x + 5; }

Bu void bir fonksiyon ve hiç bir dönüş değeri yok, yan etki yok ama kullanışsız.

    :::javascript
    function f(x) { y = 5; }

Bu da bir void fonksiyon, fakat yan etki yaratmış.

Her iki açıdan da void fonksiyon FP için mantıksız.

### Monad

Fakat yazdığımız bir çok kodda bu tarz yan etkileri yapma zorunluluğu hissediyoruz. FP'de bunun için **Monad** denilen bir kavram getiriyorlar. Monadlar kısaca yan etki yaratmadan yan etki kullanmamız gereken durumlarla başa çıkmamız için geliştirilmiş bir kavram.

Fonksiyonlar kendi ihtiyacı olan yan etkileri parametre olarak alıp müdahale ediyorlar, böylece yan etki yaratmadan aynı işlemleri yapabiliyoruz. Yani global state'de bir şey tutmadan bir state'i taşıyabiliyoruz.

    :::javascript
    function f(x, y) { return [2*x + 5, y]; }

diye bir fonksiyonumuz olduğunu varsayalım;

    :::javascript
    function g(z, y) { return [z+10, y]; }

diye de başka bir fonksiyonumuz var;

`y`nin taşımamız gereken global bir state olduğunu düşünelim;

    :::javascript
    g.apply(null, f(2, 5)) //=> [19, 5]

Bu durumu biraz irdeleyelim;

    :::javascript
    f(2, 5) //=> [9, 5]
    g(9, 5) //=> [19, 5]

görüldüğü gibi `y` değerinin state'ini taşıdık, ve yanetki kullanmadık.

Bu konuyu anlamamda [ssg'nin ekşisozlük'te yazdığı bu entry](https://eksisozluk.com/entry/22578104) epey faydalı oldu. Şimdi onun verdiği örneği JavaScript ile yazalım;

Normalde yan etki kullanarak bir toplama fonksiyonu yazalım;

    :::javascript
    var genelToplam = 0;
    function topla(x, y) {
        var toplam = x + y;
        genelToplam += toplam;
        return toplam;
    }

    topla(2, 3); //=> 5
    topla(4, 5); //=> 9
    genelToplam; //=> 14

Şimdi bu yanetkiye sahip fonksiyonu yan etkisiz hale getirelim;

    :::javascript
    function topla(x, y, genelToplam) {
        var toplam = x + y;
        genelToplam += toplam;
        return [toplam, genelToplam];
    }

    function genelToplam() {
        return topla(2, 3, topla(4, 5, 0)[1])[1];
    }

    genelToplam() //=> 14

*Tabii bu noktada çok fazla kafa karıştıran şey var, bunları iyice anlamak gerekiyor ve ben de şu an henüz öğrenme aşamasındayım :)*

### Kompozisyon

Biraz önce birer `f` ve `g` fonksiyonu tanımlamıştık;

    :::javascript
    function f(x) { return 2*x + 5; }
    function g(x) { return x+10; }

Fonksiyonel programlamada farklı fonksiyonları `compose` ederek yeni bir fonksiyon elde edebiliyorsunuz, yine aynı matematikteki gibi;

    :::javascript
    function gof(x) {
        return g(f(x));
    }

    gof(3) //=> 21

`g o f` fonksiyonu `g` ve `f` fonksiyonunun bir kompozisyonu olmuş oldu.

### Özyineleme (Recursion) ve Döngüler

Bu tarz Türkçeleştirmeye karşıyım ama kullanayım dedim. :) Fonksiyonel programlamada yan etki sevilmediği için `for` döngüsü veya `while` gibi imparatif deyimler çok mantığa uymaz. Dolayısıyla bu tarz döngüleri `recursion` ile yaratırız.

Bir faktöriyel fonksiyonunu imparative şekilde yazalım;

    :::javascript
    function factorial(x) {
        var fact = 1;
        for (var i = 1; i <= x; i++) {
            fact *= i;
        }
        return fact;
    }

Şimdi bunu recursion kullanarak yazalım;

    :::javascript
    function factorial(x) {
        if (x == 0) return 1;
        return x * factorial(x - 1);
    }

Böylece aynı işlemi fonksiyonel olarak yazmış olduk.

## JavaScript'te Fonksiyonel Programlama

Fonksiyonların first-class olması (yani Number, String gibi üst seviye olması) JavaScript'le fonksiyonel programlama yapılabilmesine oldukça fayda sağlıyor. JavaScript'te sıkça kullandığımız **anonim fonksiyon** kavramı, first-class fonksiyonlardır. Fonksiyonun oluşturulduktan sonra o anda/daha sonra çalıştırılabilmesi, hatta o anda oluşturulup çalıştırılıp çağrılabilmesini sağlar. Bu noktada "fonksiyon kapsamı" programın genelinin bir parçası olur ve problemlerin çözümünde farklı bir bakış açısı getirir. Ayrıca fonksiyonların aynı veri türleri gibi başka bir fonksiyon tarafından dönmesi de muhtemel hale gelir.

Aynı zamanda *currying, binding* gibi fonksiyonel işlemleri de yapabilir hale gelirsiniz.

Mesela:

    :::javascript
    var plus = function (x, z) {
        if (!z) z = 0;
        return function(y) { z = x + y; return [plus(z), z]; }
    }

    // 2 + 3 + 4 + 6 + 10 = 25
    plus(2)(3)[0](4)[0](6)[0](10)[1] //=> 25


*Eloquent JavaScript (Marijn Haverbeke)* isimli kitaptan bir alıntı yapacağım, örneği biraz değiştirdim, bu örnekte JavaScript'in fonksiyonel olarak nasıl kullanılacağına dair bir fikir edinebiliyoruz;

    :::javascript
    function reduce(combine, base, array) {
        array.forEach(function (element) {
            base = combine(base, element);
        });
        return base;
    }

    function add(a, b) {
        return a + b;
    }

    function sum(numbers) {
        return reduce(add, 0, numbers);
    }

Burada hiçbir yan etki yaratmadan toplama fonksiyonu oluşturduk:

    :::javascript
    sum([4, 5, 6]) //=> 15

Neyse ki, JavaScript zaten `reduce` fonksiyonunu bize sağlıyor, bu yüzden tekrar implemente etmemize gerek yok. Dolayısıyla, `sum` fonksiyonunu JavaScript'te daha hızlı ve kolay şu şekilde hazırlayabiliriz:

    :::javascript
    function sum(numbers) {
        return numbers.reduce(add, 0);
    }

veya `add` fonksiyonunu da anonim hale getirelim;

    :::javascript
    function sum(numbers) {
        return numbers.reduce(function(a, b) {return a + b}, 0);
    }

### `map`, `reduce` ile Fonksiyonel Faktöriyel Örneği

JavaScript'te `Array` kullanarak bir `sequence` oluşturabilirsiniz.

    :::javascript
    Array(1, 2, 3) //=> [1, 2, 3]
    Array(10) //=> [undefined x 10]

Dolayısıyla

    :::javascript
    Array.apply([], Array(2)); // [undefined, undefined]

şeklinde bir değer oluşturacaktır.

    :::javascript
    Array.apply([], Array(3)).map(function (x, i) {return i;}); //=> [0, 1, 2]

şeklinde bir değer oluşturur. Yani hızlıca istenilen uzunlukta bir dizi oluşturmak JavaScript'te fonksiyonel olarak çok kolay.

    :::javascript
    function range(n) {
        return Array.apply([], Array(n)).map(function (x, i) {return i;});
    }

    range(5) //=> [0, 1, 2, 3, 4]

Şimdi de bu fonksiyonu faktöriyel hesaplamak için kullanmaya çalışalım;

    :::javascript
    function factorial(n) {
        return range(n)
            .map(function (num) {return num + 1;})
            .reduce(function (num, all) {return num * all;});
    }

fonksiyonel olarak bir aralık oluşturduk, daha sonra bu aralıktan çıkan her değeri `map` ile 1 artırdık ve `reduce` ile çarparak faktöriyel hesaplamış olduk.

### Sonuçeeeeeeef

Volkan Özçelik'in Facebook'ta bu posta [yazdığı yorum da](https://www.facebook.com/groups/nodejstr/permalink/602876939750748/?comment_id=603558219682620&offset=0&total_comments=4) çok güzel açıkçası:

> En basitinden **promises** bir fonsiyonel programlama örneğidir. *(Promise aslında "monad" fonksiyonel yapısının JavaScript'e uyarlanmış halidir)*. Monad'lar da "adana dürüm"un programlama dünyasında yansımasıdır (bkz: [http://chrisdone.com/posts/monads-are-burritos](http://chrisdone.com/posts/monads-are-burritos)). Onun dışında **Underscore**, **jQuery** ve pek çok diğer kütüphane fonksiyonel programlamayı kendi içinde kullanır. En basitinden ajax çağrılarında kullandığımız "callback"ler aslında *"continuation passing style"* dediğimiz fonksiyonel yapının bir uygulamasıdır. Daha da basiti, "fonksiyon" gördüğünüz ve fonksiyonların birinci sınıf vatandaş olduğu her dilde, bilerek ya da bilmeyerek fonsiyonel programlama yapıyorsunuzdur. Daha kısa cevap: Evet genel olarak matematiksel ve "state" içermeyen işlemlerde kullanılır fonsksiyonel programlama; bununla birlikte, Haskell gibi "state" içeren dosya okuma / IO işlemleri vb.'ni "monad"lar yardımıyla fonksiyonel dünyaya map'leyip fonksiyonel programala yapan diller de var. **Fonksiyonel diller "purely functional" ve "functional with side effects" diller olmak üzere ikiye ayrılır.**

Fonksiyonel programlama problem çözümüne yeni bir bakış açısı katmakla beraber, günlük sorunlarımızı nasıl çözebileceğimiz konusunda da fikir veriyor. Tabii **multi-paradigma** gözüyle bakarak iyi yönlerini almaya çalışmakta fayda var.

Gördüğünüz hataları yorum olarak yazarsanız çok mutlu olurum. :)
