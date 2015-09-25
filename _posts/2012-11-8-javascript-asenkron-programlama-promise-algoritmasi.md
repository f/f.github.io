---
layout: post
Title: JavaScript Asenkron Programlama - Promise Monad
abstract: Promise Monad, asenkron programlamanın temel taşlarından birisidir ve fonksiyonel olarak bir Monaddır.
---

## Promise Monad'ı Nedir?

Promise, asenkron programlamanın temel taşlarından birisidir. İlk okuduğum kaynaklarda mantığını tam olarak anlamamış olsam da
daha sonradan aslında pek de zor bir konu olmadığını öğrendim.

Promise'in en büyük özelliği fonksiyonel olması ve fonksyionel programlamada Monad olarak değerlendirilmesidir. (Nesnel düşünen yazılımcılar için anlaması cidden zor)

Promise, adı üzerinde "söz" bir fonksiyonun bir olayı gerçekleyeceğine söz vermesidir. Böylece asenkron bir yapı elde edebilirsiniz, burada tabii fonksiyonel programlamanın nimetlerinden de faydalanıyoruz; mesela fonksiyonların birinci sınıf vatandaş olması gibi.

## Kendimizden örnek

Gerçek dünyadan bir promise örneği düşünelim;

**"Ali ekmek alacak, Veli de süt alacak"** gibi bir ilkokula giriş cümlesi ile başlayalım.

Bu cümlede Ali, bize ekmek alacağı sözünü veriyor; Veli ise süt alacağı sözünü veriyor.

**"Ali ekmek alırsa, ona para veririm."**

**"Veli süt alamazsa, ona çok kızarım."**

şeklinde iki adet de verilen sözlerin karşılığında yapılacak işleri düşünelim.

## Sanki çok "if/else" gibi?

Evet, buraya kadar işler fazlasıyla "if" koşulu gibi duruyor, fakat "if" koşulu asenkron bir mantıkta çalışmaz. Ayrıca, bu çok fazla **imperative** bir yapıdır. Ayrıca asenkron değil. Dediğimiz gibi, fonksiyonların blocklara göre en önemli farkı daha sonradan kullanılabilir olmaları.

Bunu if ile yazsaydık;

{% highlight js %}
if (aliEkmekAlsin()) {
  paraVer();
}

if (!veliSutAlsin()) {
  cokKiz();
}
{% endhighlight %}

Burada işlemlerin bitmesi gerektiğini görüyoruz; promise yapılarda bu işlerin bitmesi gerekmiyor. Her iş bitişi için bir promise return ediyoruz. **Imperative** değil **fonksiyonel** bir yapı elde ediyoruz.

Bunu **asenkron** düşündüğümüzde şöyle bir yapıda yazabiliriz;

{% highlight js %}
aliEkmekAlsin().success(function () {
  paraVer();
});

veliSutAlsin().fail(function () {
  cokKiz();
});
{% endhighlight %}

`If` koşullamasından biraz kurtulduk. Peki bu şekilde yazmak bize ne kazandırdı? Öncelikle, `if` kullanmadığımız için, işlemin sonucunu beklemiyoruz, çok uzun sürse dahi.
Bunun yerine yapılacak işlemin sonucuna bağlı birer **callback** atıyoruz.

## Monad: Promise

Evet, geldik işin en can alıcı kısmına. Bu fonksiyonlar `.success`, `.fail` gibi durumları nasıl dönüyorlar?

{% highlight js %}
yap().success(function () {
  baskaBirSeyYap();
});
{% endhighlight %}

Cevap: **promise** ile. Bu fonksiyonlar, implemente edilirken, `promise` dönecek şekilde yazılıyorlar.

Bakalım `aliEkmekAlsin` fonksiyonumuz nasıl implemente edilmiş?

{% highlight js %}
aliEkmekAlsin = function () {
  var deferred = new promise();

  $.ajax('/bakkal/ekmek', {}, {
    success: deferred.resolve,
    error: deferred.reject
  });
  return deferred;
};
{% endhighlight %}

Evet! Ali bakkaldan ekmek alırsa, promise çözülsün (sözünü tutmuş olsun), yoksa promise iptal olsun (sözünü tutmadı :()

## Ups! işler karıştı?

Hayır, karışmadı. Şimdi bunu daha çalışabilir bir hale getirelim:

{% highlight js %}
aliEkmekAlsin = function () {
  var deferred = new promise();
  setTimeout(deferred.resolve, 2000);

  // promise methodlarını kullanmamız gerekiyor, yoksa sonradan callback bağlayamayız.
  return deferred;
}

aliEkmekAlsin().success(function () {
  alert('aferin ali!'); // para vermiyoruz tabii ki:P
});
{% endhighlight %}

O zaman bunu çalıştıracak promise sınıfımızın en ilkel halini yazalım:

{% highlight js %}
function promise() {
  var self = this;
  var success = function() {};
  var fail = function () {};

  this.resolve = function() {
    success();
  };
  this.reject = function() {
    fail();
  };

  this.success = function (_success) {
    success = _success;
    return self;
  };
  this.fail = function (_fail) {
    fail = fail;
    return self;
  };
}
{% endhighlight %}

Bütün temeli bu kadar. Gerçekten.

Şu an bir promise sınıfına sahibiz. Fakat biraz fazla yeteneksiz. Geliştireceğiz.

Öncelikle bunun bir hatası var, yalnızca bir `success` callback'i alabiliyor. Bunu biraz daha birikebilir hale getirelim:

{% highlight js %}
function promise() {
  var self = this;
  var success = [];
  var fail = [];

  this.resolve = function() {
    var i = 0;
    while (_success = success[i++]) {
      _success();
    }
  };
  this.reject = function() {
    var i = 0;
    while (_fail = fail[i++]) {
      _fail();
    }
  };

  this.success = function (_success) {
    success.push(_success);
    return self;
  };
  this.fail = function (_fail) {
    fail.push(_fail);
    return self;
  };
}
{% endhighlight %}

Evet işte oldu. Bu bizim promise algoritmamızın temelini oluşturuyor. Ve aslında çalışan bir promise algoritması. :)

## Birden fazla deferred'i yönetmek

Evet, orası için de fikrimiz var.

Elimizde bir sürü deferred (promise dönen) nesne olsun;

{% highlight js %}
when (aliEkmekAlsin(), veliSutAlsin()).success(function () {
  alert("aferin ali ve veli!");
});
{% endhighlight %}

diyerek alabilmeliyiz;

O halde hemen when algoritmasının en ilkel halini yazalım:

{% highlight js %}
function when(ilk, ikinci) {
  var self = this;
  var resolved = 0;

  var success = [];
  var fail = [];

  var successOrFail = function () {
    var _success, _fail;
    var i = 0;
    if (resolved == 2) {
      while (_success = success[i++]) {
        _success();
      }
    } else {
      while (_fail = fail[i++]) {
        _fail();
      }
    }
  };

  ilk.success(function () {
    resolved++;
    successOrFail();
  }).fail(function() {
    successOrFail();
  });

  ikinci.success(function () {
    resolved++;
    successOrFail();
  });

  this.success = function (_success) {
    success.push(_success);
    return self;
  };
  this.fail = function (_fail) {
    fail.push(_fail);
    return self;
  };
  return this;
}
{% endhighlight %}

Biraz uzun oldu, ama en temel hali bu. Kısaca şunu yapıyor, her promise'a bir success callback atayıp, bu callback'lerin bir pointer'ı (resolved) bir artırıyor. Böylece kaç adet resolve olduğunu biliyoruz.
Eğer resolved ile toplam promise sayısı eşit ise, bu işlem başarılı oluyor. Aksi halde başarısız olarak dönüyor.

{% highlight js %}
when (aliEkmekAlsin(), veliSutAlsin()).success(function () {
  alert("aferin ali ve veli!");
});
{% endhighlight %}

artık çalışır durumda.

Fakat bu örnek yalnızca iki deferred için çalışıyor. O zaman bunu biraz daha dinamik yapalım.

{% highlight js %}
function when() {
  var self = this;
  var resolved = 0;

  var args = arguments;
  var deferreds = Array.prototype.slice.call(args);

  var success = [];
  var fail = [];

  var successOrFail = function () {
    var _success, _fail;
    var i = 0;
    if (resolved == deferreds.length) {
      while (_success = success[i++]) {
        _success();
      }
    } else {
      while (_fail = fail[i++]) {
        _fail();
      }
    }
  };

  var i = 0;
  while (deferred = deferreds[i++]) {
    deferred.success(function () {
      resolved++;
      successOrFail();
    });
  }

  this.success = function (_success) {
    success.push(_success);
    return self;
  };
  this.fail = function (_fail) {
    fail.push(_fail);
    return self;
  };
  return this;
}
{% endhighlight %}

Şimdi deneyelim:

{% highlight js %}
when (aliEkmekAlsin(), veliSutAlsin(), isikIlikSutIcsin()).success(function () {
  alert("aferin ali ve veli!");
});
{% endhighlight %}

Evet, çalışıyor. :)

Promise işleri bu kadar kolay. Tabii, bunun daha güçlü implementasyonlarını yazmak mümkün. Eğer jQuery kullanıyorsanız, hazır implemente edilmiş bir hali zaten var.

Fonksiyonel programlama ile ilgili kaynakları araştırmanızı da öneririm.
