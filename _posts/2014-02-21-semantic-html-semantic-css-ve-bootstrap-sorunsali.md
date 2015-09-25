---
layout: post
title: Semantic HTML, Semantic CSS ve Bootstrap Sorunsalı
abstract: HTML'de anlamsallık, presentational sınıflardan kurtulmak ve Bootstrap'ın hatalı kullanımlarının yarattığı problemler.
---

Uzun zamandır bu konuda yazmak isteyip de yazamıyordum. Bugün HackerNews'te [Using Bootstrap the Right (Semantic) Way](http://www.ostraining.com/blog/coding/bootstrap-right-way/) başlıklı yazıyı görünce bu yazıyı yazmaya karar verdim.

Yazıda da belirtildiği gibi, eskiden tüm HTML kodlarımız **presentational** idi. Sonra HTML'in gelişimiyle birlikte bu yapılar birer birer **semantic** olmaya başladılar. Peki bu **presentational** ve **semantic** arasındaki fark ne ve neden **presentational** HTML kötü?

Öncelikle **w3c'nin** class isimleriyle ilgili yazdığı şu uyarıya bakalım: [http://www.w3.org/QA/Tips/goodclassnames](http://www.w3.org/QA/Tips/goodclassnames)

> **Good names**
>
> `warning`, `important`,  `downloadableImage` and `submenu` are all good names. They describe what a certain element represents, and they are not likely to change. A warning will always remain a warning, no matter how much the look of the page changes.
>
> **Bad names**
>
> `border4px`, `lighttext` and  `prettybackground` are examples of bad names. You might fatten that border to a whopping 5 pixels, or the background may look pretty old after a while, and not pretty at all. An advantage of using CSS is that you won't have to change much in order to change the looks of your website. If you have to change all light text into dark text, and thus change all classes  lighttext to darktext in all your HTML pages, you're likely to miss a few.

## Presentational HTML ve CSS sınıfları?

**Presentational** kelimesi belki **sunumla alakalı** gibi çevrilebilir. Yani HTML'in içerisinde kullanıcıya nasıl sunulacağına dair bir bilgi veriyorsunuz. Peki bu ne kadar doğru, ve gerekli mi?

Eskiden hatırlarsınız şöyle tag'lerimiz vardı:

	:::html
	<a><font size="11px" color="red"></font></a>

Semantic olmadıkları için kullanımdan kaldırıldılar. Aynı zamanda `b`, `i`, `u` gibi stile sahip presentational tag'ler da tedavülden çıktı. Hatta tarayıcılar bu presentational tagleri kendileri stillendirdikleri için `reset.css` sahibi de olduk.

Ama ihtiyaçlar değişmedi; yeni nesil web evrimleşti:

	:::html
	<a class="big red button"></a>

şeklinde aynı kodu yazmaya devam ettik.

Buna **Object Oriented CSS** diyoruz. *Aslında sevdiğim de bir yöntem*. CSS'e daha fazla iş veriyoruz, HTML'in presentational etkisini **biraz da olsa** alıyoruz. Peki, bu ne kadar doğru? Tamamen doğru mu?

Bu kodda, bir `a` tag'ine `big red button` sınıfı vererek bir buton elde ediyoruz. Bunun presentational olarak `<a><font size="11px" color="red"></font></a>` -modern olması dışında- bundan farkı var mı? Bence pek yok.

Presentational HTML'le ilgili esas sorun şu:

> **Yarın bu buton mavi olacaksa ne yapıyoruz?**
>
> Cevap: Bütün kaynak koddaki `red` olan kodları bulup `blue` yapıyoruz.

Ama takımımızdaki daha hızlı iş çözen genius arkadaşımız durur mu; yapıştırır cevabı:

> Genius Cevap: Bütün kaynak koddaki `red` olan classları `blue` yapacağımıza,
> `.red { background-color: red; }` olan yeri `.red { background-color: blue; }` yaparız!

Projemiz bir task sayesinde darmadağan oluverdi. Tabii bu çok uç bir örnek olabilir ama biz neler görmedik ki!

Bunun yerine

	:::html
	<a class="primary"></a>

dersek, gayet semantik ve genius arkadaşımızın istediği hızda, kod kalitesini bozmadan bir kod yazmış oluyoruz.

### HTML'i Değiştirmemek

HTML dediğimiz şey; adı üzerinde **Markup Language**. Yani **İşaret dili**. Gerçekten **semantik** bir algıda HTML'in işaretlemekten başka bir iş yapıyor olması **anlamsız**. Yani **semantik** değil.

	:::html
	<div class="header-red balloon-image">
		<button class="pie-radius red largescreen">Error!</button>
	</div>

`class` değerleri içindeki kodlar stil belirtiyor ve HTML'in stil belirtmek gibi (presentation) bir görevi yok. HTML semantik olarak işaretlemekten başka bir şey yapmamalı.

	:::html
	<header>
		<button class="error">Error!</button>
	</header>

Geri kalan stil kısmını CSS'te nasıl yapmak istiyorsanız öyle yapın. Stil kısmı CSS'e ait, HTML'e değil!

### Grid Presentation

Ve yepyeni bir akım: **Grid**. Evet belli hizalama sistemlerine sahip olmak mükemmel bir şey. Fakat, en popüler grid kullanım şekli olan tarzda kullanımla, HTML'de şunları görmek ne kadar doğru?

	:::html
	<header class="pull-3 span-4">
		<h1 class="fluid-span-11"></h1>
	</header>

`header`i genişletmek için HTML yazacağız. Neden? Stil CSS'in işi değil miydi?

### `.clearfix` Presentation

En lazım olan classlardan birisi; fakat **presentational**. Bunu nasıl çözmeli? `clearfix` de yine tamamen stillerle alakalı ve HTML'de olması kadar **anlamsız** bir şey daha yok.

## Kurtarıcılar: SASS & LESS & Stylus Mixinleri

Çözüm gözümüzün hemen önünde aslında. SASS & LESS gibi Mixin destekleyen üst CSS dilleri kullanarak bu konuyu rahatça çözebiliyoruz ve semantik, geliştirilebilir yapılar elde ediyoruz.

	:::html
	<header>
		<h1></h1>
	</header>

HTML yapısına LESS (veya diğerleri ile) aşağıdaki gibi bir kod yazarsak;

	:::css
	header {
		.pull(3)
		.span(4)
		h1 {
			.fluid()
			.span(11)
			.clearfix()
		}
	}

sorunu düzeltmiş oluyoruz. Kodumuz tamamen semantik, ve daha **düzenlenebilir** bir hale geldi. HTML hiç bir şekilde stil taşımıyor.

## Bootstrap'ın Tehlikeli Kullanımı

Bootstrap çok hızlı iş çıkmasını sağlayan çok güzel tasarlanmış bir framework, bu konuda şüphemiz yok. Fakat geliştiriciler bunu ne kadar doğru kullanıyor; Bootstrap'ın semnatic'e bakışı nasıl?

	:::html
	<div class="row">
  		<div class="col-xs-12 col-md-8"></div>
  		<div class="col-xs-6 col-md-4"></div>
	</div>

Bu kodu Bootstrap'ın dökümanından aldım. HTML'de representation'un dibine vurduk. **Medium'da 8 birim genlişlikten 4 birim genişliğe çekeceksek** gidip HTML'de değişiklik yapıyoruz.

`col-md-8`in **w3c**'nin **bad names** dediği `border4px` class isminden hiç bir farkı yok.

	<div class="row">
  		<div class="col-sm-5 col-md-6"></div>
  		<div class="col-sm-5 col-sm-offset-2 col-md-6 col-md-offset-0"></div>
	</div>

	<div class="row">
  		<div class="col-sm-6 col-md-5 col-lg-6"></div>
  		<div class="col-sm-6 col-md-5 col-md-offset-2 col-lg-6 col-lg-offset-0"></div>
	</div>

Bu kod için konuşmak dahi istemedim açıkçası.

## Bootstrap'in Doğru Kullanımı

Neyse ki Bootstrap LESS ile yazılı bir framework. Dolayısıyla gerekli Mixin'lere sahip. Yine kendi sayfasından aldığım ve doğru olan kodu yazalım, ama önce Bootstrap'ın bu konuyla alakalı ne dediğine bakalım:

> In addition to prebuilt grid classes for **fast layouts**, Bootstrap includes Less variables and mixins for quickly generating your own **simple, semantic layouts**.

Bootstrap'ın semantik olmadığını zaten kendi dökümanından okuyabiliyorsunuz.

	:::html
	<div class="wrapper">
  		<div class="content-main"></div>
  		<div class="content-secondary"></div>
	</div>

class isimleri gayet semantik, presentational değil. Şimdi bunun Bootstrap'in sağladığı mixin'lerle yazılışına bakalım:

	:::css
	.wrapper {
	  .make-row();
	}
	.content-main {
  		.make-lg-column(8);
	}
	.content-secondary {
  		.make-lg-column(3);
  		.make-lg-column-offset(1);
	}

## Sonsöz: Bootstrap ve jQuery

jQuery ile Bootstrap'ın bir alakası yok. Fakat bu ikisiyle ilgili yakın genel bir kanı var.

> JavaScript bilmiyorum jQuery biliyorum

eskiden beri artık gelenek olmuş bir söylem. Ve yakında duyacağımız bir şeylerden biri şu olacak;

> CSS bilmiyorum ama Bootstrap biliyorum

(Bir çok back-end developer her ikisini de söylüyor hatta)

Her ikisi de oldukça tehlikeli zihinlerden çıkmış fikirler. jQuery'i veya Bootstrap'i **defacto** kabul eden ve direkt olarak bunu öğrenmeye çalışan geliştiriciler sektöre bol bol spagetti ve düzenlenemez kod göndermeye devam edecek maalesef. :)
