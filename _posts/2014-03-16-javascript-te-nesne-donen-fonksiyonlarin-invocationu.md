---
layout: post
title: JavaScript'te Nesne Dönen Fonksiyonların Instance Invocation'unda Prototipin Değişimi
abstract: "Douglas Crockford'un \"JavaScript: The Good Parts\"ından daha önce bilmediğim, farketmediğim bir detay öğrendim."
---

Selamlar,

Bu aralar [Douglas Crockford'un "JavaScript: The Good Parts"][1]ını okuyorum. Daha önce okumamış olmamın verdiği utançla buraya yazmaktan da çekindim aslında :)

Kitabın "function invocation" ile alakalı kısmında Crockford ilginç bir detaydan bahsediyor:

> If the function was invoked with the `new` prefix and the `return` value is not an object, then `this` (the new object) is returned instead.

Burada ilgimi çeken detay "*`return` value is not an object*" kısmı oldu. Hemen neymiş ne değilmiş denemek istedim, çünkü bilmediğim bir özellikti.

Normalde, bildiğiniz gibi, bir fonksiyon "`new`" keyword'ü ile çağırılırsa ilgili fonksiyonun `prototype` nesnesine bir link dönüyor ve bu sayede klasik OOP gibi çalışabiliyoruz.

    :::javascript
    function Foo() {
        // construction
    }
    Foo.prototype.hello = "world";

Normalde bu durumda hepimizin bildiği gibi, eğer `Foo` fonksiyonunu `new` ile çağırırsak çıktıda `hello` değerine erişebileceğiz.

    :::javascript
    var bar = new Foo();
    console.log(bar.hello); //=> "world"

Eğer başka herhangi bir değer dönersek yine de `prototype` dönmeye devam edecekti.

    :::javascript
    function Foo() {
        return "baz";
    }
    Foo.prototype.hello = "world";

Eğer `new` ile çağırırsak,

    :::javascript
    var bar = new Foo();
    console.log(bar); //=> {hello: "world"}

Eğer normal olarak çağırırsak,

    :::javascript
    var bar = Foo();
    console.log(bar); //=> "baz"

çıktısı alacağız. Yani, fonksiyon bir string dönerse JavaScript bunu dikkate almayacak ve yine "prototype" nesnesini dönecek.

Şimdi gelelim esas noktaya;

    :::javascript
    function Foo() {
        return {foo: "bar"};
    }
    Foo.prototype.hello = "world";

Bu durumda bugüne kadar bana "bunu `new` ile invoke edersen ne döner" diye sorsanız, "fonksiyon'un prototype'ı" derdim fakat öyle değilmiş :)

    :::javascript
    var bar = new Foo();
    console.log(bar.hello); //=> undefined
    console.log(bar); //=> {foo: "bar"}

Eğer nesne dönmek zorundaysam ve "string" bir nesne değilse, bu noktada şunu denemek ilk aklıma gelen şey oldu:

    :::javascript
    function Foo() {
        return new String("baz");
    }
    console.log(new Foo()); //=> {0: "b", 1: "a", 2: "z", length: 3}

Bu noktada gerçekten string dönebiliyoruz, fakat "`toString`" kullanarak birleştirmek gerekiyor.

    :::javascript
    console.log(new Foo().toString()); //=> "baz"

`this` nesnesinin `new` nesnesine göre nasıl farklı invokasyona uğradığına dair bir deneme daha yapalım:

    :::javascript
    function Foo() {
        return [this, "baz"];
    }
    Foo.prototype.hello = "world";

    // new kullanmadan invokasyon
    var bar = Foo();
    console.log(bar); //=> [Window, "baz"]

    // new kullanarak invokasyon
    var bar = new Foo();
    console.log(bar); //=> [Foo {hello: "world"}, "baz"]

Küçük bir detay. Muhtemelen kitabı okuyan bir çok arkadaşın çok önceden bildiği bir durumdu, fakat bilmenin gerçekten faydalı olabileceği noktalar olacağına eminim.

  [1]: http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742
