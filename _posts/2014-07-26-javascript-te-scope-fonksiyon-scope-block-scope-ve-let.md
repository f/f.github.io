---
layout: post
title: "JavaScript'te Scope: Fonksiyon Scope, Block Scope ve 'let'"
abstract: Geçen haftalarda verdiğim bir eğitimde *"JavaScript'te fonksiyon scope"* konusunu işlerken *block scope* özelliğin gerçekten var olup olmaması gerektiğini de sorgulamıştık. Açıkçası şimdiye kadar yapılan işlere bakınca çok da ölümcül bir gerekliliği olmasa da hataları azaltmak adına kullanımının doğru olabileceğini düşünüyorum. Bu noktada da block scope'a biraz bakalım ve nasıl uygulayabileceğimizi gözden geçirelim.
---

Geçen haftalarda verdiğim bir eğitimde *"JavaScript'te fonksiyon scope"* konusunu işlerken *block scope* özelliğin gerçekten var olup olmaması gerektiğini de sorgulamıştık. Açıkçası şimdiye kadar yapılan işlere bakınca çok da ölümcül bir gerekliliği olmasa da hataları azaltmak adına kullanımının doğru olabileceğini düşünüyorum. Bu noktada da block scope'a biraz bakalım ve nasıl uygulayabileceğimizi gözden geçirelim.

Bu aralar **ES.next** ve **ES6** ile içli dışlıyım. Açıkçası uzun zamandır **CoffeeScript** kullandığım için **ECMAScript 6**'nın bir çok özelliğini de kullanıyorum. Fakat CoffeeScript fonksiyon scope'a güzel katkılar yapsa da *(fat-arrow gibi)*, block scope konusunda bir etkisi yok. *(Hatta bu yüzden bunu bir macro ile çözebileceğimi düşünüp [Macaron][1] adında bir de ufak eklenti geliştirdim, daha önce yazılan bir eklentiyi biraz daha geliştirdim diyelim)*

## JavaScript'te Scope

Efendim, JavaScript'te **block scope diye bir olay yok.** Böyle başlayalım ki kafa karışıklığı oluşmasın.

### Peki, block scope ne demek?

Block scope, temel olarak `{` ve `}` arasında kalan kodun sadece o araya ait olması olarak tanımlanabilir.

Hemen örnekle açıklayalım;

{% highlight js %}
var x = 10;

if (true) {
    var x = 20;
    console.log(x); //=> 20
}

console.log(x); //=> 20
{% endhighlight %}

Burada `x` iki kere tanımlanıyor (declaration) ve ikincisi bir **if scope'u** içinde olmasına rağmen *(JavaScript'te doğal olarak)* `x`'i değiştirdi. Ayrıca ufak da bir hata yaptık, zira iki kez `var x` tanımlanıyor.

Bu sorunu normalde nasıl çözüyoruz?

### Fonksiyon Scope

{% highlight js %}
var x = 10;

if (true) {
    (function () {
        var x = 20;
        console.log(x); //=> 20
    })();
}

console.log(x); //=> 10
{% endhighlight %}

Burada JavaScript'in fonksiyon scope özelliğini kullanarak bir **IIFE** *(Immediately Invoked Function Expression)* oluşturduk. Böylece o kısım sanki bir *block scope'muş gibi* ayrıldı.

### Fonksiyon Scope'un Sorunları

Fonksiyon scope ne kadar güzel ve bir çok açıdan işimizi çözse de, bazı noktalarda işimize engel olabiliyor.

#### Context Karmaşası

Bunlardan birisi `context` karmaşası:

{% highlight js %}
function Hello() {
    var x = 10;
    this.y = "hello";

    if (true) {
        (function () {
            var x = 20;
            console.log(x, this.y); //=> 20, undefined
        })();
    }

    console.log(x, this.y); //=> 10, "hello"
}
new Hello();
{% endhighlight %}

Bunun sebebi ise her oluşan **fonksiyon scope'unun ayrıca bir de context oluşturması**. Bu noktada işi çözmek için **context binding** işini de devreye sokmamız gerekiyor:

{% highlight js %}
...
...
        (function () {
            var x = 20;
            console.log(x, this.y); //=> 20, "hello"
        }).call(this); // context binding yaparak çağırıyoruz. `apply` da kullanabilirdik.
...
...
{% endhighlight %}

#### Döngü Deyimlerinin Etkisizliği

Fonksiyon scope ayrıca `break`, `continue` gibi deyimleri de etkisiz hale getiriyor.

{% highlight js %}
function Hello() {
    var x = 10;
    this.y = "hello";

    for (var i = 0; i < 10; i++) {
        (function () {
            var x = 20 + i;
            if (i == 3) {
                break; // Error: Illegal break statement
            }
        })();
        console.log(i);
    }
}
new Hello();
{% endhighlight %}

Bu işlemin sonucunda `Error: Illegal break statement` şeklinde bir hata aldık, **çünkü oradaki IIFE bir döngü içerisinde olduğunun farkında değil.**

Ne yazık ki bunun için Fonksiyonları kullanarak `context binding` gibi bir çözüm yok. Tek yapabileceğiniz *`for` döngüsünü fonksiyon scope'u içerisine almak*.

{% highlight js %}
...
    (function () {
        for (var i = 0; i < 10; i++) {
            var x = 20 + i;
            if (i == 3) {
                break; // Error: Illegal break statement
            }
            console.log(i);
        }
    })();
...
{% endhighlight %}

### JavaScript'te Block Scope

Block scope'un gerekliliğini biraz anladıysak şimdi de **ECMAScript 6**'da nasıl implemente edildiğine bakalım.

#### `let`

{% highlight js %}
var x = 10;

if (true) {
    let x = 20;
    console.log(x); //=> 20
}

console.log(x); //=> 10
{% endhighlight %}

Bu kadar basit, yukarıda bir ton çözüm aradığımız şey, `let` ile rahatlıkla çözülebiliyor. Böylece yalnızca o scope içerisinde bir değişken tanımlayabiliyoruz.

#### JavaScript'te Block Scope'u Taklit Etmek

Yukarıda **IIFE** ile yaklaştığımız yapıya `try / catch` kullanarak daha yakın taklit edebiliyoruz. Bu yöntem ne kadar `hacky` olsa da **Google Traceur Compiler** da aynı yöntemi kullanıyor.

{% highlight js %}
var x = 10;
var y = 20;

if (true) {
    try { throw undefined; } catch (x) { // let x;
    try { throw undefined; } catch (y) { // let y;
        x = 30;
        y = 40;
        console.log(x); //=> 30
        console.log(y); //=> 40
    }}
}

console.log(x); //=> 10
console.log(y); //=> 20
{% endhighlight %}

Burada yeni bir fonksiyon oluşturmadığımız için context (`this`) hiç bir zaman değişmeyecek. Şimdi bir de döngü içerisinde kullanalım:

{% highlight js %}
function Hello() {
    var x = 10;
    this.y = "hello";

    for (var i = 0; i < 10; i++) {

        try { throw undefined; } catch (x) { // let x;

            x = 20 + i;
            if (i == 3) {
                break;
            }
            console.log(x, this.y); //=> 20.. 21.. 22, "hello"..
        }
        console.log(i); //=> 0.. 1.. 2..
    }
    console.log(x); //=> 10
}
new Hello();
{% endhighlight %}

Biraz karmaşık olsa da, **Traceur Compiler** veya **let-er** kullanarak bu yöntemi oluşturabilirsiniz, ayrıca *Makro* kullanarak da bu yapıyı kurabilirsiniz.

### Kaynaklar

- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
- [https://github.com/google/traceur-compiler](https://github.com/google/traceur-compiler)
- [https://github.com/getify/let-er](https://github.com/getify/let-er)

[1]: http://github.com/f/macaron
