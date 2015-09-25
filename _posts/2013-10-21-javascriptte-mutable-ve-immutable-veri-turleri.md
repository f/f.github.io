---
layout: post
title: JavaScript'te Mutable ve Immutable Veri Türleri
abstract: JavaScript'in Mutable ve Immutable veri türleriyle alakalı bir kaç not.
---

Uzun zamandır blog post yazmıyorum; bugün çok basit bir ispat blog postu yazayım dedim. :)

Bir programlama dilinde bir verinin construct olduğu değeri değiştiremiyorsanız bu değere **immutable**, değiştirebiliyorsanız **mutable** diyoruz.

{% highlight js %}
var x = "fka";
x[1] = "K";
console.log(x) //=> "fka"
{% endhighlight %}

gördüğünüz gibi `x` değişkeni hiçbir şekilde değişikliğe uğrayamadı.

JavaScript'te iki çeşit veri tipi mutable'dır:

  - Object
  - Array

Diğer veri türleri ise immutable veri türlerine girerler.

  - undefined
  - null
  - Boolean
  - Number
  - String

Bunu ispatlamak için basit bir işlem yapıyoruz:

{% highlight js %}
var x = new Object({0: "f", 1: "k", 2: "a"});
x[1] = "K";
{% endhighlight %}

Bu `x` değerine bakalım:

{% highlight js %}
x.valueOf(); //=> {0: "f", 1: "K", 2: "a"}
{% endhighlight %}

Şimdi aynı kodu `String` fonksiyonu ile yazalım:

{% highlight js %}
var x = new String({0: "f", 1: "k", 2: "a"});
x[1] = "K";
{% endhighlight %}

Şimdi `x` değerinin değiştireceğini düşünsek de immutable olduğu için bu şekilde kaldı.

{% highlight js %}
x.valueOf(); //=> "fka"
{% endhighlight %}

Bir objeyi eğer daha sonradan immutable yapmak istiyorsanız `Object.freeze` kullanabilirsiniz;

{% highlight js %}
var x = {0: "f", 1: "k", 2: "a"};
Object.freeze(x);

x[1] = "K";

x.valueOf(); // {0: "f", 1: "k", 2: "a"};
{% endhighlight %}

## +=, *=, ... Operatörleri

`+=` gibi operatörler her ne kadar nesneyi değiştiriyor gibi görünse de aslında yeni bir nesne construct ediyor:

{% highlight js %}
var x = "fka";
x+="dev";
console.log(x); //=> "fkadev"
{% endhighlight %}

bu aslında şu şekilde düşünülmeli:

{% highlight js %}
var x = "fka";
x = x + "dev";
{% endhighlight %}

daha da açarsak;

{% highlight js %}
var x = "fka";
x = new String(x) + new String("dev");
{% endhighlight %}

yani aslında veriyi mutate etmiyor (değiştirmiyor) onu da kullanarak yeni bir primitif değer atıyoruz.

Basit bir bilgi, sadece bu şekilde anlatınca anlaşılması kolay olduğuna inandığım için yazmak istedim. :)
