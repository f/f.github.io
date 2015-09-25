---
layout: post
title: JavaScript Observable Design Pattern
abstract: "JavaScript’in müthiş event yeteneğini hepimiz zaten biliyoruz, (bana domino taşlarını hatırlattığı için bu resmi seçtim). Backbone’da, jQuery’de vb. gelişmiş tüm kütüphanelerde sıkça kullanılan Observer/Observable tasarım şablonu ile ilgili biraz bilgi vermek istedim."
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

![Dominos](http://dl.dropbox.com/u/20947008/Blog/domino.png)

JavaScript’in müthiş event yeteneğini hepimiz zaten biliyoruz, (bana domino taşlarını hatırlattığı için bu resmi seçtim). Backbone’da, jQuery’de vb.
gelişmiş tüm kütüphanelerde sıkça kullanılan Observer/Observable tasarım şablonu ile ilgili biraz bilgi vermek istedim.

Dediğim gibi, event’lar JavaScript’in en büyük silahı. Bazen öyle durumlar oluyor ki, yazdığımız kodlar bir yerlerden event alabilir, bir şeyleri hook
edebilir, tetikleyebilir olması gerekiyor. Bu kodun tekrar kullanılabilirliğini ve okunabilirliğini en yüksek seviyeye çekiyor. Yorumlarla satır satır
ne yaptığımı ve Observable nesnelerin nasıl oluşturulduğunu anlattım.

{% highlight js %}
// Nesnelerin eventları olmasını sağlar.
var Observable = function() {
    // private event nesnesi.
    var events = {};
    // Yeni bir event tanımlanıyor.
    this.on = function(name, func) {
        // Eğer hiç yoksa, bir adet boş tanımlanmalı.
        if (!events[name]) events[name] = [];
        // İçeriye koyuyoruz.
        events[name].push(func);
    };
    // Tetikleyici method, tetiklenen tüm eventleri çalıştırır.
    this.trigger = function(name, data) {
        // Bu isimde bir event var mıydı?
        if (events[name] && events[name].length > 0) {
            // Tümüne bak
            for (var i=0, l = events[name].length; i < l; i++) {
                // Gönderilen veriyi pasla. context ise nesnenin kendisi olmalı.
                events[name][i].call(this, data);
            }
        }
    };
    // Tüm eventları veya belirli eventları kapatmak için.
    this.off = function(name, func) {
        var l = arguments.length;
        // Eğer hiç argüman yoksa, tüm eventlar kapanır.
        if (l == 0) {
            // Event kapatması demek, tüm events literalinin boşaltılması demektir.
            events = {};
        // Eğer sadece bir isimdeki bir event off edilecekse,
        } else if (l == 1 && events[name]) {
            // O events içerisiden silinir.
            delete events[name];
        // Eğer belli bir fonksiyon hedeflenmişse, o aranır ve silinir.
        } else if (l == 2 && events[name] && events[name].length > 0) {
            for (var i = 0, _l = events[name].length; i < _l; i++) {
                if (events[name][i] == func) {
                    delete events[name][i];
                    // fin.
                    return;
                }
            }
        }
    }
};

var Uye = {
    name: 'fatih',
    set: function(field, value) {
        var old = this[field]||'';
        this[field] = value;
        this.trigger('set:'+field, {old: old});
    }
};
// Observable methodlarını bağlıyoruz.
Observable.call(Uye);
// Event tanımlıyoruz.
Uye.on('set:name', function(data) {
    alert('Uye adi eski: ' + data.old + ', yeni: ' + this.name)
});
// Event tanımlıyoruz.
Uye.on('set:surname', function(data) {
    alert('Uye soyadi eski: ' + data.old + ', yeni: ' + this.surname)
});
Uye.set('name','kadir');
Uye.set('surname','akin');
{% endhighlight %}

Aşağıda çalışan örneğini görebilirsiniz:

<iframe frameborder="0" src="http://jsfiddle.net/fkadev/fw77s/embedded/"></iframe>

Gördüğünüz gibi, Observable.call diyerek bir nesneyi event bağlanabilir hale getirdik, daha sonra set methodumuza bir trigger bağladık.
Bu trigger sayesinde set methodu her çalıştığında üzerine bağlı tüm eventlar tetiklenecek. Ve tabii, tetiklenmesini istediğimiz eventları biz tanımlıyoruz.

İşleyişin genelini kodun içerisinde anlattım, sorularınız olursa sormaktan çekinmeyin,

Çok teşekkürler. :)
