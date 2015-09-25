---
layout: post
title: "Object Oriented Javascript'e Giriş: Interface, Abstract Sınıflar, Getter ve Setter"
abstract: "Nesne Yönelimli JavaScript'e giriş Dersleri, 4. Bölüm: Interface, Abstract Sınıflar, Getter ve Setter"
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Merhaba,

Bir önceki yazımda prototip tabanlı kalıtımın nasıl yapıldığından bahsetmiştim. Kısaca bir kalıtımlı sınıf yaratalım.

<iframe src="http://jsfiddle.net/fkadev/Kg6BU/embedded/js,result"></iframe>

Gördüğümüz gibi önceki bilgilerimize dayanarak yeni bir sınıf yaratmış olduk. Şimdi standart OOP’de ihtiyacımız olan
interface yapısını sağlıyor olmamız gerekiyor. Peki bunu nasıl yapabiliriz?

OOP’de interfaceler çoğu dilde varken, Javascriptte yoktur. Fakat implementasyonu sağlanabilir. Çünkü kontrol edilebilecek
kadar kolay bir yapıya sahiptir. Bunun için `Function` sınıfına genel bir `implements` methodu ekleriz, ve bu method
kendisine belli methodları araştırır. Basitçe bir örnek yapalım:

<iframe src="http://jsfiddle.net/fkadev/QxAyj/1/embedded/js,result"></iframe>

Gördüğümüz gibi aslında bir interface yapısı oluşturmak çok zor değil. Şimdi bu yazdığımız yapıya bir de `extends` methodu ekleyerek,
daha önce yaptığımız `extend` işlemini kolaylaştıralım, böylece daha kolay bir sınıf mekanizması kurmuş olacağız. Bir de method ve
property ekleme işlemlerimizi daha kolaylaştıran yeni bir method ekleyelim, bunun adı da `proto` olsun, çünkü sınıfımızın genel
yapısını aslında bu oluşturacak. Demin yaptığımız implementasyon methodunda biraz değişiklik yapalım, ve prototypelar eklendikten
sonra çalışmasını sağlayalım. `implements` methodu yalnızca atamaları yaparken, `ensureImplements` methodu ile implementasyon kontrolünü
sağlayalım. Ayrıca `super` adında bir getter yaratarak, üst sınıfa ulaşmasını sağlayabiliriz. Bakalım nasıl bir şey ortaya çıktı:

<iframe src="http://jsfiddle.net/fkadev/QxAyj/10/embedded/js,result"></iframe>

Uzunca bir kod yazdığımız ortada. Fakat yukarıda yazdığım bir kısmı JavaScript’e OOP desteği vermek için kullanacağız. Şimdi gelelim bir kaç bilmediğimiz konuya:

## Getter ve Setter

**_Not: getter / setter özelliği deprecated ve standart dışı özelliklerdir, ancak bazı browserlarda çalışır. ES5 standart dışında tutmuştur,
yalnızca JavaScript’in diğer Object Oriented dillerin yapabildiklerini yapabildiğini göstermek amacıyla anlatılmıştır, dolayısıyla bu kullanımı
standart olarak kabul etmeyiniz._**

PHP yazıyorsanız bilirsiniz, `__get` ve `__set` magic fonksiyonlar vardır. Bir `prototype`’a erişmeyi sağlar fakat aslında fonksiyon çalışıyordur.
Bu özelliğin aynısı JavaScript'te de var. Yukarıdaki kütüphanemizde yaptığımız örneğe bakalım:

<iframe src="http://jsfiddle.net/fkadev/6T2HN/embedded/js,result"></iframe>

Evet kodun bu kısmında gördüğümüz gibi, `this.super` bize verildiği fonksiyondan elde ettiği sonucu verecektir. Hemen çalışan bir örnek yapalım o halde:

<iframe src="http://jsfiddle.net/fkadev/6T2HN/1/embedded/js,result"></iframe>

Şimdi işin çılgın noktasına geldik. :) Olay şu ki, getter tanımladığınız bir değişkeni, setter tanımlamadıkça değiştiremezsiniz, JS hata verecektir.
Setter tanımladığınızı da getter tanımlamadıkça geri alamazsınız. İşte olayın güzelliği burada zaten. Getter ve Setter method tanımlaması bu kadar,
şimdi geldik Abstract olayının nasıl yapıldığına.

Yukarıdaki kütüphanede abstract sınıfların nasıl çalıştığını anladınız mı bilmiyorum fakat, bir fonksiyonun en başına `this.abstract(arguments);`
şeklinde bir kod koyuyoruz. Abstract (soyut) sınıfların temel mantığı, bir şeyin atası olmadıkça kendisinden yaratılamaz. Bu mantıktan yola çıkarak,
bir sınıfı çağıran fonksiyonun, kendisiyle aynı olup olmadığı bize abstract olarak çağırılma durumunu verebilir. Yine bir örnekle gösterelim:

<iframe src="http://jsfiddle.net/fkadev/6T2HN/2/embedded/js,result"></iframe>

Abstract’ın temel mantığı bu şekilde, yukarıdaki kodda `this.constructor` nesnenin constructorunu, `arguments.callee` ise kendisini verecektir.
Bildğimiz gibi kalıtım yaparken, `this.constructor`’u bir alt sınıf eziyor, dolayısıyla bu eşitlik sağlanmadığından hata vermiyor. yukarıdaki
kütüphanede uygulanmış halini bulmanız mümkün. Gelelim son anlatacağımız konu olan `apply` olayına.

PHP’de `call_user_func` methodunu bilirsiniz. Bu method ile bir başka fonksiyonu parametre vererek çağırabilirsiniz, veya bir fonksiyona
alias tanımlayabilirsiniz. Bir methoda gelen tüm değerleri aynen başka bir fonksiyona kanalize edebilirsiniz. Hemen bir örnek gösterelim:

<iframe src="http://jsfiddle.net/fkadev/6T2HN/3/embedded/js,result"></iframe>

Gördüğünüz gibi, `testAlias` fonksiyonu test fonksiyonuna tüm veriyi kanalize ediyor ve alias gibi çalışıyor. Yukarıdaki kütüphanede
constructorların birbirlerine nasıl iletişim kurduklarını inceleyin :)

Buraya kadar her şeyi sorunsuz anladıysanız, artık temel seviye Object Oriented Javascript biliyorsunuz demektir. Sonraki postlarımda
artık daha ileri seviye işlemler, design patternlar (evet çok heyecan verici bence de) ve MVC (bu da bir design pattern tabi) ile ilgili
örnekler yapmaya çalışmayı düşünüyorum :) MVC’den önce design patterns anlatmak daha doğru olacaktır kanaatindeyim. Sorularınız olursa
info {@} fatihkadirakin.com adresine mail atabilirsiniz, veya alt taraftaki yorum kısmından ulaşabilirsiniz.

Umarım sizin için faydalı olmuştur.

Teşekkürler.

## OOP Desteği Örnek Kod

{% highlight js %}
//Unique array fonksiyonu
Array.prototype.unique = function() {
    var unique = [];
    for (var i = 0; i < this.length; i++) {
        for (var j = i + 1; j < this.length; j++) {
            if (this[i] === this[j]) j = ++i;
        }
        unique.push(this[i]);
    }
    return unique;
};

//Interface atama fonksiyonu
Function.prototype.implements = function(interfaces) {
    var _implements = [];
    for (var i = 0; i < arguments.length; i++) {
        _implements = _implements.concat(arguments[i]);
    }
    _implements = _implements.unique();
    this.__implements__ = _implements;

    //chaining
    return this;
};

//Implementasyon fonksiyonu
Function.prototype.ensureImplements = function(interfaces) {
    var _implements = [];
    for (var i = 0; i < arguments.length; i++) {
        _implements = _implements.concat(arguments[i]);
    }
    _implements = _implements.unique();
    for (var i = 0; i < _implements.length; i++) {
        if (!(_implements[i] in this) && !(_implements[i] in this.prototype)) {
            throw new Error('Implementation failed, ' + _implements[i] + ' should be implemented');
        }
    }
};

//Kalıtım fonksiyonu
Function.prototype.extends = function(parent) {
    this.prototype = Object.create(parent.prototype);
    this.prototype.constructor = this;
    //chaining
    return this;
};

//Prototype ekleme fonksiyonu
Function.prototype.proto = function(structure) {

    for (var item in structure)
    this.prototype[item] = structure[item];

    //super methoda erişim
    this.prototype.__defineGetter__('super', function() {
        return this.__proto__.__proto__;
    });

    //statiklere erişim
    this.prototype.__defineGetter__('self', function() {
        return this.constructor;
    });

    //private method.
    this.prototype.abstract = function() {
        if (this.constructor == arguments[0].callee)
          throw new Error('You cannot create instance of an abstract class.');
    };

    //implementasyon kontrol
    this.ensureImplements(this.__implements__);

    return this;
};
{% endhighlight %}
