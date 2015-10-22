---
layout: post
title: TypeScript ve Babel.js Kullanarak Typed-ES7 Kullanmak
abstract: JavaScript gelişiyor. Bu durumda biz de sürekli öğreniyoruz. TypeScript'le birlikte ECMAScript 7'nin özelliklerini bugünden itibaren nasıl kullanacağımızı inceleyelim.
---

Merhaba!

1 sene sonra ilk blog yazısı yazıyorum. Bu sürede **askere** gidip gelmiş bulundum.
Askerlik oldukça farklı bir deneyim, ama bugün anlatacağım şey askerlik değil tabii ki.

## ECMAScript 7

Henüz yeni yeni ES6 özelliklerini öğreniyorduk ki, ES7'nin özellikleri de ortaya çıkmaya
başladı. Ve bu gelişmelerin tümü biz *Eski JavaScript, yeni ECMAScript* geliştiricilerini
heyecandan heyecana sürüklüyor.

Bugün 'ES6'da ES7'de neler var' detaylarına girebilecek kadar zaman yaratamadım.
Onun yerine **TypeScript** ve **Babel.js** kullanarak bu özellikleri bugünden kullanmaya
nasıl başlayabileceğimizi inceleyelim.

## TypeScript ne alaka?

Evet tüm bu işlemleri aslında sadece **Babel** kullanarak `--stage 0` ile rahatlıkla halledebilirdik.

TypeScript'i `--noEmit` ile yalnızca **type check** işlemleri için kullanıyoruz. **Babel**'in ise tüm transpile
işinden sorumlu olması daha makul geldi.

## Örnek Kod

Örnek bir ES7 kodu yazalım:

{% highlight js %}
(async function () {
	await sleep(3000);
	console.log("selam!");
})();
{% endhighlight %}

Bu kod, basitçe `async` ve `await` kullanarak asenkron bekleme yaratıyor.
Bunun için öncelikle `sleep` fonksiyonunu tanımlıyoruz. Bu fonksiyonumuza
bir de **type** desteği ekleyelim ve `time` parametresinin bir `Number` olması
gerektiğini belirtelim.

{% highlight js %}
function sleep(time: Number) { //<= time:Number diyerek TypeScript'e kontrol edebileceği bir veri tanımlıyoruz.
	return new Promise((resolve) => {
		setTimeout(resolve, time);
	});
}
{% endhighlight %}

Kodumuzun son hali (`test.ts`)

{% highlight js %}
function sleep(time: Number) {
	return new Promise((resolve) => {
		setTimeout(resolve, time);
	});
}

(async function () {
	await sleep(3000);
	console.log("selam!");
})();
{% endhighlight %}

Bu kodu önce `tsc` ile derleyelim:

{% highlight bash %}
tsc --experimentalAsyncFunctions --experimentalDecorators --target es6 test.ts --noEmit
{% endhighlight %}

Bu kod `--noEmit` ile sadece statik kontrol yapacak fakat kodu herhangi bir yere çıktı vermeyecek.

Bu kodun çıktı verememesini sağlayalım:

{% highlight js %}
(async function () {
	await sleep("3000"); //<== Number olması gerekirken, string verdik.
	console.log("selam!");
})();
{% endhighlight %}

{% highlight bash %}
hello.ts(8,14): error TS2345: Argument of type 'string' is not assignable to parameter of type 'Number'.
{% endhighlight %}

hatasını alıyoruz.

Bu kodu geri çevirip **Babel** ile derlemeye devam edelim:

{% highlight bash %}
babel --optional runtime --stage 0 test.ts -o test.js
node test.ts
{% endhighlight %}

> `--optional runtime` kullanmak için `babel-runtime` paketini kurmayı unutmayın.

## Oneliner

İşimiz daha kolay olsun, çok uğraşmayalım diye bir de oneliner yazalım.

{% highlight bash %}
tsc --experimentalAsyncFunctions --experimentalDecorators --target es6 test.ts --noEmit && babel --optional runtime --stage 0 test.ts -o test.js
{% endhighlight %}

Hatta bu kodu biraz daha genişleterek birden çok dosyayı çevirmesini sağlayalım:

{% highlight bash %}
tsc --experimentalAsyncFunctions --experimentalDecorators --target es6 *.ts --noEmit && babel --optional runtime --stage 0 *.ts -d es5
{% endhighlight %}

Sevgiler.
