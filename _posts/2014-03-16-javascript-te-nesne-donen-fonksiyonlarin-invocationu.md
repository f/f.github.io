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

{% highlight js %}
    function Foo() {
        // construction
    }
    Foo.prototype.hello = "world";
{% endhighlight %}


Normalde bu durumda hepimizin bildiği gibi, eğer `Foo` fonksiyonunu `new` ile çağırırsak çıktıda `hello` değerine erişebileceğiz.

{% highlight js %}
    var bar = new Foo();
    console.log(bar.hello); //=> "world"
{% endhighlight %}


Eğer başka herhangi bir değer dönersek yine de `prototype` dönmeye devam edecekti.

{% highlight js %}
    function Foo() {
        return "baz";
    }
    Foo.prototype.hello = "world";
{% endhighlight %}


Eğer `new` ile çağırırsak,

{% highlight js %}
    var bar = new Foo();
    console.log(bar); //=> {hello: "world"}
{% endhighlight %}


Eğer normal olarak çağırırsak,

{% highlight js %}
    var bar = Foo();
    console.log(bar); //=> "baz"
{% endhighlight %}


çıktısı alacağız. Yani, fonksiyon bir string dönerse JavaScript bunu dikkate almayacak ve yine "prototype" nesnesini dönecek.

Şimdi gelelim esas noktaya;

{% highlight js %}
    function Foo() {
        return {foo: "bar"};
    }
    Foo.prototype.hello = "world";
{% endhighlight %}


Bu durumda bugüne kadar bana "bunu `new` ile invoke edersen ne döner" diye sorsanız, "fonksiyon'un prototype'ı" derdim fakat öyle değilmiş :)

{% highlight js %}
    var bar = new Foo();
    console.log(bar.hello); //=> undefined
    console.log(bar); //=> {foo: "bar"}
{% endhighlight %}


Eğer nesne dönmek zorundaysam ve "string" bir nesne değilse, bu noktada şunu denemek ilk aklıma gelen şey oldu:

{% highlight js %}
    function Foo() {
        return new String("baz");
    }
    console.log(new Foo()); //=> {0: "b", 1: "a", 2: "z", length: 3}
{% endhighlight %}


Bu noktada gerçekten string dönebiliyoruz, fakat "`toString`" kullanarak birleştirmek gerekiyor.

{% highlight js %}
    console.log(new Foo().toString()); //=> "baz"
{% endhighlight %}


`this` nesnesinin `new` nesnesine göre nasıl farklı invokasyona uğradığına dair bir deneme daha yapalım:

{% highlight js %}
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
{% endhighlight %}


Küçük bir detay. Muhtemelen kitabı okuyan bir çok arkadaşın çok önceden bildiği bir durumdu, fakat bilmenin gerçekten faydalı olabileceği noktalar olacağına eminim.

  [1]: http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742
