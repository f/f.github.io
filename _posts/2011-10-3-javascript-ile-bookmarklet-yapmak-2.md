---
layout: post
title: "JavaScript ile Bookmarklet Yapmak - 2: Efektif Kullanım & Örnek Twitter Bookmarklet"
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Merhaba,

Bir önceki konuda Bookmarklet yapmayı anlatmıştık. Temel olarak bir bookmarklet’in nasıl çalıştığını gördük.

{% highlight js %}
javascript:(function(bm){if(!window.bmlet)window.bmlet={};if(!window.bmlet[bm]){window.bmlet[bm]
=true;var s=document.createElement('script');s.onerror=function(){window.bmlet[bm]=false;};s.src
=bm;document.getElementsByTagName('head')[0].appendChild(s);}}('http://f.cl.ly/items/0X2q3N2n1V0
i0B0R3X20/bookmarklet.js'));
{% endhighlight %}

linkinin bir bookmarklet oluşturmak için temel olduğunu gördük. Şimdi anlatacaklarım ise bu yapıyı nasıl
en efektif şekilde kullanabileceğimizle ilgili olacak.

Evet, yukarıdaki kod parçacığı (link) üzerinden giderek iki farklı olaya değineceğiz.

Bookmarklet’lar temelde iki şekilde çalışırlar. Bazıları bookmarklet’e tıkladığınızda bir kere çalışır,
bazısı ise her tıkladığınızda çalışırlar. Mesela, sayfayı paylaşan bir bookmarklet bir kere tıklandığında
çalışır, fakat seçilen metni paylaşıyorsanız durum farklılaşır. Çünkü her seçim farklı bir paylaşım olabilir
vesaire.

O halde, eklememiz gereken bir **cache** sistemi olmalı. Yukarıdaki kod parçacığı `window.bmlet` değeri içerisine
bookmarkletleri toplar, bu yüzden bir kere bu listeye girdiyse tekrar girmez. Bu basitçe bir cache sistemidir.
Belki bu koda bir şeyler ekleyeceğimizi düşünmüş olabilirsiniz fakat, bu kodda değişiklik yapmayacağız.
Bu bizim temel bookmarklet linkimiz. Eğer cache yapmak istemiyorsanız, js dosyasının sonuna `Math.random().toString()`
gibi bir değer eklemeniz yeterli olacaktır.

Şimdi örnek bir bookmarklet yazalım. Bu bookmarklet temel olarak Tweet gönderecek. Eğer sayfada seçili bir
metin varsa onu, yoksa boş bir Tweet penceresi açılacak. Bir küçük özelliği daha olsun dedim ve “Tweet Selected”
diye bir buton ismi belirledim, bu yalnızca eğer seçili metin varsa görüntülenecek.

<iframe src="http://jsfiddle.net/pUPmx/2/embedded/js,result"></iframe>

Basitçe bir twitter bookmarklet yazdık. Şimdi bunun çalışan bir versiyonunu deneyelim.

<iframe src="http://jsfiddle.net/d4648/1/embedded/js,result"></iframe>

Bu kodu şimdi linkleştirip deneyelim:

{% highlight js %}
javascript:(function(bm){if(!window.bmlet)window.bmlet={};if(!window.bmlet[bm]){window.bmlet[bm]
=true;var s=document.createElement('script');s.onerror=function(){window.bmlet[bm]=false;};s.src
=bm;document.getElementsByTagName('head')[0].appendChild(s);}}('https://raw.github.com/gist/2ef2
16215c516cdf4a0f/2137c66040360bb1c9259645f6865a3a18a37f20/bookmarklet.js'));
{% endhighlight %}

Bunu bookmark olarak ekleyelim:

![Bookmarklet-1](http://f.cl.ly/items/2R3D3N260I1z1x3i390G/Screen%20Shot%202011-09-08%20at%2002.14.00.png)

Sonra da bir test sürüşüne çıkalım.

![Bookmarklet-2](http://f.cl.ly/items/0Q1H1c0j1T251q3o3s3G/Screen%20Shot%202011-09-08%20at%2002.20.52.png)

Basitçe bir Bookmarklet yapmayı öğrendik, bunu biraz daha düzenleyebilmemiz mümkün tabii. Bu yalnızca bir örnek :)

Öğrendiğimiz gibi, bookmarkletler yalnızca JavaScript'ten ibaretler. Ve bir de JavaScript linklerinden.

Umarım faydalı bir yazı dizisi olmuştur. Soru sormaktan kaçınmayın.

Teşekkürler.
