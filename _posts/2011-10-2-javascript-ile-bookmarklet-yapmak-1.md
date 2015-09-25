---
layout: post
title: "JavaScript ile Bookmarklet Yapmak - 1: Sayfa İçerisinde Kod Çalıştırmak ve Uzaktan JavaScript Dosyası Çağırmak"
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Merhaba,

Bugün yeni bir konuya değinmek istedim. Yeni sosyal paylaşım platformlarında sık sık görmekte olduğumuz bir yapı: “bookmarklet”

Bookmarkletler temelde birer linktirler. Yalnızca http şeması yerine javascript şemasını kullandıklarından browserlar standart olarak `javascript:` ile başlayan linkleri
javascript kodu olarak değerlendirir ve çalıştırır. Aşağıdaki örnekteki kullanımı bilmeyeniniz yoktur:

<iframe src="http://jsfiddle.net/fkadev/ucTww/embedded/html,result"></iframe>

Gördüğümüz gibi `onclick` gibi bir event tanımlamadan href attribute üzerinden javascript çalıştırdık.
Bildiğimiz gibi href attribute bir link alır ve sayfayı yönlendirir. Peki mantıklı düşünüp şöyle yapalım:

![Bookmarklet-1](http://f.cl.ly/items/1S1g1M2s2F3K2M0B1F15/ln1.png)

Enter’a basıyoruz ve:

![Bookmarklet-2](http://f.cl.ly/items/0o0y0J1h2w3B2U1n3V10/Screen%20Shot%202011-09-02%20at%2000.14.53.png)

Gördüğümüz gibi bir `alert` penceresi açıldı. Demek ki adres çubuğu üzerinden JavaScript kodu çalıştırmamız mümkün. Bunu görmüş olduk.

Şimdi tekrar mantıklı düşünelim, bildiğimiz gibi bookmarklar aslında birer Title - Link çiftleridir. Bir başlığı ve bir linki vardır. Başlığa tıkladığımızda browser linki açar.

Brainstorm: Verilenler... İstenenler... Hmm... O öyleyse bu böyle falan filaan... Bulduk!

Demek ki `javascript:alert('test');` gibi bir linki Bookmark olarak ekleyebiliriz :)

Hemen deneyip yeni bir bookmark ekleyelim. Title kısmına istediğiniz bir başlık ve link kısmına da `javascript:alert('test');` yazalım.

Not: Çoğu browserda adres çubuğundaki linki bookmark bar’a sürükleyerek de bookmark eklemeniz mümkün.

Eklediğimiz bookmark’a tıklayalım, görüldüğü gibi ilk bookmarkletimizi yazdık :)

## Biraz Hareket

Evet deminki örneğimiz epey basit oldu. Estetiği bile yok :)

JavaScript yazarken en çok kullandığımız librarylerden biri genellikle **jQuery**. Bookmarklet yazarken de jQuery’den faydalanmamız
işimizi hızlandırmak açısından oldukça gerekli. Fakat bookmarklet’lar herhangi bir site üzerinde çalıştığı için,
sitelerin jQuery kullanıp kullanmadıklarını bilemiyoruz.

<iframe src="http://jsfiddle.net/fkadev/W8SmB/1/embedded/html,result"></iframe>

Evet gördüğümüz gibi

{% highlight js %}
    javascript:void(alert(window.jQuery?'Var':'Yok'))
{% endhighlight %}

linkini kullanarak sayfada jQuery olup olmadığını gösteren bir başka bookmarklet yazdık. O halde yeni bir
bookmarklet yazalım ve bu bookmarklet bulunduğu siteye jQuery desteği eklesin :)

<iframe src="http://jsfiddle.net/fkadev/TBwry/embedded/js,result"></iframe>

Evet, gördüğümüz gibi sayfaya jQuery desteği ekleyen bir başka kod bu, bunu bookmarklet olarak yazarsak:

{% highlight js %}
javascript:(function(jq){if(!jq){var s=document.createElement('script');s.src='http://code.jqu
ery.com/jquery-latest.min.js';document.getElementsByTagName('head')[0].appendChild(s);jQuery.n
oConflict();}}(window.jQuery));
{% endhighlight %}

şeklinde bir kodumuz olacak. `jQuery.noConflict();` yapmamızın sebebi ise, kullanıcının mevcut sisteminde zaten adı `$`
olan bir başka fonksiyon olabilir. Bookmarklet’in bu mevcut yapıya zarar vermiyor olması gerekir.

Görüldüğü gibi, basit bir bookmarklet yaparak sayfaya jQuery desteği eklemeyi öğrendik. Burada aslında
öğrenmemiz gereken jQuery desteği eklemek değil, dışarıdan bir javascript dosyasının sisteme çağrılabilme
yeteneği. JavaScript dosyasını çağırdığınızda artık kullanıcının sayfasına erişebilir hale geliyorsunuz ve
tüm kapılar açılıyor :)

{% highlight js %}
javascript:(function(bm){if(!window.bmlet)window.bmlet={};if(!window.bmlet[bm]){window.bmlet[bm]
=true;var s=document.createElement('script');s.onerror=function(){window.bmlet[bm]=false;};s.src
=bm;document.getElementsByTagName('head')[0].appendChild(s);}}('http://f.cl.ly/items/0X2q3N2n1V0
i0B0R3X20/bookmarklet.js'));
{% endhighlight %}

Bu kod temel bir bookmarklet kodu. Son parametrede verilen kodu bir kez çalıştırıp bırakıyor.
Tabii dolayısıyla burada istediğimizi yapmakta özgür oluyoruz.

Bu bookmarklet kodunun açık hali ise şu şekilde:

<iframe src="http://jsfiddle.net/fkadev/TBwry/1/embedded/js,result"></iframe>

Bugünlük bu kadar, konunun devam postunda örnek bir bookmarklet yapacağız.
