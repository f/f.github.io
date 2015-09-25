---
layout: post
title: "Koding Vol. 2.1: Uygulama Geliştirmeye Giriş"
abstract: "Geliştiriciler için yepyeni bir platform: localhost'a \"güle güle\". 2. bölümde uygulama geliştirmeye başlıyoruz."
---

Daha önce Koding ile ilgili bir yazı paylaşmıştım. İkinci bölümde ise Koding'te nasıl uygulama geliştirebileceğimizi göreceğiz.

## Koding ile Uygulama Geliştirmeye Giriş

Temel bir bilgi verdiğimize göre Koding ile uygulama geliştirme konusunda bir şeyler yapmaya başlayabiliriz.

### Uygulama Oluşturmak

Koding'te uygulama oluşturmanın mantığı çok kolay,

Uygulama oluşturmak için **Develop** altındaki **Make a new App** butonuna tıklamanız gerekiyor.

![Make a new App](http://cl.ly/image/3y2a1213313A/Screen%20Shot%202013-02-15%20at%206.10.07%20PM.png)

Açılan pencereden "Blank Application" seçiyoruz. Bir diğer seçenek ise "Sample Application" Sample.kdapp'ten bir klon yaratıyor ve ismine de sizin belirlediğiniz bir ismi veriyor.

![Create a new Application](http://cl.ly/image/2d3X1s453A1W/Screen%20Shot%202013-02-15%20at%206.11.45%20PM.png)

Burada **Name** kısmına kendi belirlediğimiz *boşluk içermeyen* bir isim girilmesi gerekiyor.

Bizim uygulamamızın adı ***"HelloWorld"*** olsun.

HelloWorld isimli uygulamamızı oluşturduktan sonra aşağıdaki gibi bir uygulamaya sahip olacağız:

![Hello World app](http://cl.ly/image/0X1m2i3X3T18/Screen%20Shot%202013-02-15%20at%206.18.12%20PM.png)

Filetree'mizde uygulamamız şu şekilde:

![Hello World app in Filetree](http://cl.ly/image/29322v2G222h/Screen%20Shot%202013-02-15%20at%206.19.37%20PM.png)

#### Uygulama Manifestosu: `.manifest`

Her uygulamanın bir manifestosu, bildirisi var. Bu bildiriler uygulama hakkında bilgiler içeriyor.

Manifest dosyasının içeriği de şu şekilde:

{% highlight js %}
{
  "devMode": true,
  "version": "0.1",
  "name": "HelloWorld",
  "identifier": "com.koding.apps.helloworld",
  "path": "~/Applications/HelloWorld.kdapp",
  "homepage": "kullaniciadi.koding.com/helloworld",
  "author": "Kullanici Adiniz",
  "repository": "git://github.com/kullaniciadi/helloworld.kdapp.git",
  "description": "HelloWorld : a Koding application created with the blank template.",
  "category": "web-app",
  "source": {
    "blocks": {
      "app": {
        "files": [
          "./index.coffee"
        ]
      }
    },
    "stylesheets": [
      "./resources/style.css"
    ]
  },
  "options": {
    "type": "tab"
  },
  "icns": {
    "128": "./resources/icon.128.png"
  }
}
{% endhighlight %}

Bu manifesto bizim uygulamamızın temel bilgilerini oluşturuyor. Dosyaların neler olduğu uygulamamızın adı, versiyonu, geliştirme modunda olup olmadığı gibi bir çok bilgi mevcut. Şimdilik ismini *HelloWorld* yerine *Hello World* yapalım:

{% highlight js %}
"name": "HelloWorld"
{% endhighlight %}

satırını

{% highlight js %}
"name": "Hello World"
{% endhighlight %}

olarak değiştiriyoruz ve kaydediyoruz. Editörün sağ üst köşesindeki butonlarla bu işlemi gerçekleştirebilirsiniz.

![Save, Compile and Run](http://cl.ly/image/1b272a2q103p/Screen%20Shot%202013-02-15%20at%206.23.58%20PM.png)

Dosyayı kaydettikten sonra, **Compile & Run** butonuna tıklıyoruz. Bu işlem uygulamamızı yeniden derliyor ve çalıştırıyor.

![Hello World tab](http://cl.ly/image/4042423E3d2D/Screen%20Shot%202013-02-15%20at%206.26.03%20PM.png)

Yeni bir tabda uygulamamız açıldı, fakat isminin *Hello World* değil hala *HelloWorld* olduğunu görüyoruz. Bunun için uygulama listesinin olduğu tab altında sağ üst köşedeki **Refresh Apps** butonuna tıklamamız gerekiyor. Bu buton uygulamaları yeniliyor ve manifestolarındaki değişiklikleri güncelliyor.

![Refresh Apps](http://cl.ly/image/2E1a0F071Q1Q/Screen%20Shot%202013-02-15%20at%206.26.30%20PM.png)

Artık uygulamamızın adının **Hello World** olduğunu görebiliyoruz:

![Rename App](http://cl.ly/image/1s3z2F3k0X1y/Screen%20Shot%202013-02-15%20at%206.29.22%20PM.png)

#### Uygulama ve `appView`

Uygulama açıldıktan sonra karşımızda kalbimiz kadar temiz bir ekranla karşılaşıyoruz:

![appView](http://cl.ly/image/3b2O123d1K0A/Screen%20Shot%202013-02-15%20at%206.33.38%20PM.png)

![index.coffee](http://cl.ly/image/0G1G373R1q3R/Screen%20Shot%202013-02-15%20at%206.36.21%20PM.png)

`index.coffee` bizim ana dosyamız. Bu dosyayı açtığımızda içerisinde:

{% highlight coffeescript %}
do ->
{% endhighlight %}

şeklinde bir kodla karşı karşıya kalıyoruz. Bunun JavaScript'çesi:

{% highlight js %}
(function() {

})();
{% endhighlight %}

Yani bir scope açılmış ve sizin bir şeyler yazmanızı bekliyor. Bu durumda bu isteği boş çevirmemek gerekiyor.

Şimdi önemli bir noktaya geldik. Burada fazla derine inmeden yüzeysel geçmem gerekiyor, çünkü konu bir anda KDFramework dökümanlarına dönebilir, ki bu zaten var ve amacım bundan bir tane daha yazmak değil. Fatih Arslan, oldukça güzel bir tane yazdı zaten. :)

Her uygulama scope'u içerisinde `appView` adında bir view mevcut. Bu view bir KDView nesnesi ve KDView'e göre bir geliştirme izlememiz gerekiyor. Bu durumda KDFramework'ü incelemek gerekiyor. Bunun yerine ben **hızlıca bir başlangıç** sağlamanız için bir şablon oluşturdum, sizin yapacağınız da aşağı yukarı aynı şablonu oluşturmak.

{% highlight coffeescript %}
class MainView extends JView
  constructor:->
    super

    # Uygulama kodu buraya…

  pistachio:->
    """
    Buraya HTML/Template Kodu…
    """
  viewAppended: ->
    @setTemplate do @pistachio

appView.addSubView new MainView
{% endhighlight %}

Bu temel bir uygulama şablonu.

Şimdi uygulamamızı biraz daha geliştirelim:

{% highlight coffeescript %}
class MainView extends JView
  constructor:->
    super
    @header = new KDHeaderView
      type: "big"
      title: "Hello World!"

  pistachio:->
    """
    {{> @header}}
    <p>
        Hello World!!!
    </p>
    """
  viewAppended: ->
    @setTemplate do @pistachio

appView.addSubView new MainView
  cssClass: "hello-world"
{% endhighlight %}

Bu kodu yazdığımızda karşımıza şu şekilde bir uygulama çıkıyor:

![Hello World](http://cl.ly/image/3P0E2s301s0X/Screen%20Shot%202013-03-16%20at%203.18.43%20AM.png)

Gördüğünüz gibi ilk uygulamamız hazır.

#### Kite'lar

Uygulamamızı yazdık fakat yapmamız gereken bir şey daha var. Bu uygulama biraz interaktif olmalı.

Kite'larımız daha önce bahsettiğimiz gibi birer kablo, birer bağlantı hattını oluşturuyor. Kite'lar ile hosting üzerinde bazı işlemleri yapıp cevap almanız mümkün.

Kite'lar teknik anlamda aynı front-end'deki Ajax gibi çalışıyorlar. Örnekte jQuery ile yapılmış bir Ajax request görüyoruz.

{% highlight js %}
$.ajax({
  url: "/myapi",
  data: {a: 1, b: 2},
  success: function() {
    // veri geldikten sonra...
  }
});
{% endhighlight %}

veya CoffeeScript ile;

{% highlight coffeescript %}
$.ajax
  url: "/myapi"
  data:
    a: 1
    b: 2
  success: ->
    // veri geldikten sonra...
{% endhighlight %}

şeklindeki bir Ajax ile `/myapi` öğesinden veri çektiğimizi hatırlayalım. Fakat bu kod server üzerinde HTTP ile çalışıyordu. Bu yapı yine Koding'in Kite'larına benziyor fakat örnekteki gibi bir kod ile çalışıyor.

{% highlight coffeescript %}
kite = KD.getSingleton "kiteController"
kite.run
  kiteName: "mykite"
  withArgs:
    a: 1
    b: 2
, (err, data)->
  // veri geldikten sonra...
{% endhighlight %}

Gördüğünüz gibi, kod neredeyse aynı.

`kite.run` methodu *`object|string, function`* şeklinde bir parametre alıyor fakat nesne yerine bir string koyarsanız o kodu kullanıcının sunucusu üzerinde /Users/fka/Dropbox/Apps/Calepin/koding-2-uygulama-gelistirme.mdçalıştırıyor.

{% highlight coffeescript %}
kite = KD.getSingleton "kiteController"
kite.run "ls", (err, data)->
  // "ls" komutunun çıktısı
{% endhighlight %}

Şimdi biraz önceki kodumuza tekrar dönelim.

{% highlight coffeescript %}
class MainView extends JView
  constructor:->
    super
    @header = new KDHeaderView
      type: "big"
      title: "Hello World!"

    kite = KD.getSingleton "kiteController"
    kite.run "ls", (err, res)=>
      @header.$().next().append res

  pistachio:->
    """
    {{> @header}}
    <pre>
    ls result:
    </pre>
    """
  viewAppended: ->
    @setTemplate do @pistachio

appView.addSubView new MainView
  cssClass: "hello-world"
{% endhighlight %}

Burada ls komutu alıp çıktısını `$()` methodunu kullanarak DOM elementine ulaştığımız `@header` değişkeninden sonraki elemente (Burada `pre` tagına) ekliyoruz. Şimdi bakalım nasıl bir sonuç elde ettik:

![Kite Run](http://cl.ly/image/2h2Q42012V1L/Screen%20Shot%202013-03-16%20at%203.37.29%20AM.png)

Bu seferlik bu kadarı yeterli, bir sonraki sefere daha yaratıcı uygulamalar, Koding'teki bir dosyayı açıp yeni bir dosyayı kaydetmek gibi konulara değineceğiz, belki küçük bir Plain Text editör yaparız. :)

Sağlıcakla!
