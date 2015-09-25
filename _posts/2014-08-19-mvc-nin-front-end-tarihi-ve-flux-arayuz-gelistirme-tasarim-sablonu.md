---
layout: post
title: MVC'nin Front-end Tarihi ve Flux Arayüz Geliştirme Tasarım Şablonu
abstract: Facebook tarafından ortaya atılan ve arayüzde MVC yerine daha kullanışlı bir yapı sunmasıyla bir çok sorunu çözen tasarım şablonu Flux'ı inceliyoruz. Flux'a giden yolda geçirdiğimiz maceralara da bakacağız.
---

Merhaba!

Son zamanlarda [Facebook'un arayüz için geliştirdiği araçları incelerken][1], gerçekten sorun çözmeye yönelik olan ve bence şu ana kadar gelmiş en iyi tool'lardan biri olan [React][2]'ı öğrenme fırsatım oldu. *React* benim için gerçekten ufuk açan bir teknoloji oldu. Zira bunu aşılayan **Üstün Özgür**'e ve **Sepeth Kern**'e teşekkürlerimi iletmiş olayım :) Bugün yine Facebook tarafından ortaya atılan **Flux**'tan biraz bahsedelim istedim.

## MVC'nin Front-end Tarihi

### MV(Controller)

MVC çok eski bir **arayüz geliştirme** tasarım şablonu ve genel olarak dönemindeki teknoloji ihtiyaçlarına binaen *daha az state içeren* işlemler için tasarlanmış. Her ne kadar basit state'leri yönetmek bu tasarım şablonunda çok kolay olsa da günümüzdeki arayüzlerde yaratılan state'lerin büyümesi bu işlerin yönetimini daha zor hale getiriyor. Bu da aslında günümüzde yaşadığımız büyük bir sorun.

*MVC*, bilinen ilk tasarım şablonlarından birisi. Temel mantığı **View** üzerinden gelen etkileşimin **Controller** üzerinden geçerek **Model**'e erişmesi ve **Model**'in bulunduğu *state*'i **Controller**'a geri bildirerek **View**'a geri taşıması.

Yani kısaca

    View > Controller > [server data] > Model > Controller > View

Bu da aslında oldukça iş gören ve çoğu zaman hayatımızı kurtaran bir çözüm. Fakat bu çözüm her ne kadar sistem taraflı yapılarda çok iş görse de tamamen client tarafında çalışan uygulamalar için biraz sıkıntılı bir durum.

Bu oluşan sorunu çözmek için tabii ki topluluklar boş durmayıp yeni şeyler icat ediyorlar. Ben de bu süreci takip eden birisi olarak **Backbone.js**'i gördüğümde "işte bu ya, bütün dertler bitti" demiştim. Ama teknoloji dünyasında bu rahatlığa hiç bir zaman ulaşamıyoruz tabii ki :) Bu noktada yeni arayışlara girildi ve ortaya yeni tasarım şablonları çıktı.

### MV(Collection)

Öncelikle **View** ve **Controller** yapılarının front-end mimarisinde birbirinden çok da farklı şeyler olamayacağını anladık. Çünkü ilk *interaktif* etkileşim kullanıcıdan View'a doğrudan geliyordu ve bunun View katmanında kararını hızlıca verebiliyorduk. Bu **Controller** yapısının yok olmasına ve View'in **template**'den farklı bir yapıya ait olduğu anlamına geliyor. *Controller* olan mimaride View'lar statik birer çıktı iken, bu mimaride View'lar DOM üzerinde çalışan ve daha akıllı, dinamik girdi ve çıktılar haline geliyorlar.

Bu noktada **Model** ve **View** katmanları tek başlarına birbirleriyle iletişim kurabilir hale geldiler.

Fakat bazı mimariler, controller'in ortadan kalkmasıyla birlikte birden fazla modeli yönetebilecek ek bir mimari ihtiyacı hissetti, çünkü **View**'in bu işi üstlenmesi *seperation of concern*'e göre **View**'e amacı dışında bir iş yüklemek olacak.

Backbone.js gibi mimariler de bu noktada **Collection** katmanını geliştirip View'i duruma göre direkt olarak Collection'a veya direkt olarak Model'e bağlamayı seçtiler. Bu bence müthiş bir fikir ve ben hala kullanıyorum.

    View > (Collection|Model) > [server data] > (Collection|Model) > View

Fakat bu patternde bir sorun vardı, tüm **DOM** işlemlerini kendiniz yönetiyordunuz ve bu da biraz fazla kod yazmak anlamına geliyordu, *daha çok kod da daha çok bug demek*. Bunu bir şekilde azaltma gereği ortaya çıktı.

Daha sonra bazı geliştiriciler **data binding** diye bir yapı ortaya attılar. **Bu yapıda veri yapısı ile DOM birbirine bağlanabiliyordu.**

### MV(ViewModel)

.. Ve ortaya **MVVM** diye bir yapı çıktı. Sanıyorum bu yapıyla ilk karşılaştığım JavaScript framework'ü **Knockout.js**. Bunun temeli ise verilerin view ile her zaman **bağlı** olmasına dayanıyor.

    View > ViewModel > [server data] > Model > View

Şimdiye kadar MVVM yapısını en iyi sağlayan araçlardan birisi de **Angular.js**.

MVVM'de **ViewModel** dediğimiz şey aslında neredeyse controller'a denk geliyor. Angular.js'deki Controller fonksiyonlarını buna benzetebiliriz.

DOM'a veri yapısı bağlamak demek, DOM'a iş vermek demek. Controller'dan aldığımız işi DOM'a yıkmak ise acaba o kadar da avantajlı mı? Bu tartışılan bir soru iken, bence DOM üzerinde *logic* tanımlamak o kadar da makul bir davranış biçimi değil.

**Kaldı ki, DOM, yani HTML, logic koyabileceğiniz en kıt ortam.** Çünkü yapısı gereği bir çok şeye izin vermemekle birlikte kodun düzenlenebilirliği de maksimum düzeyde **düşüyor**.

MVC, MVVM derken arayüz geliştirme bu noktaya geldi ve şimdi bir "es" verelim ve React'ı inceleyelim.

## Render: V(iew)

Bu noktada, geçen yıl, **Facebook** ilginç bir konsept ortaya sürerek [React][1]'ı duyurdu. Aslında Instagram'a ait olan bu mimari yapı **Angular'a çok benzemekle birlikte, hiç alakası yok**. Peki bu ne demek?

**Angular'da template logic *DOM* içerisine konulurken, React'te template logic *Virtual-DOM* içerisine konuluyor.**

#### Kısaca Virtual DOM

Bir veriyi DOM'a direkt olarak yollamak her yaptığınız değişiklikten sonra git'e push yapmak gibi. Bu ne kadar verimsizse DOM üzerinde direkt değişiklik de aynı derecede verimsiz bir hale geliyor.


{% highlight html %}
<ul data-bind="[1,2,3]">
    <li>1</li>
    <li>2</li>
    <li>3</li>
</ul>
{% endhighlight %}

gibi bir yapıda DOM üzerinde yapacağımız işlemleri düşünelim.

`data-bind="[1,2,3]"` ile bağladığımız veriyi `[1,3,5]` olarak değiştirelim. Bu bağı gören ve **DOM üzerinde işlem yapan** bir framework (Angular.js gibi) şunu yapacak:

- İçerideki her veriyi silecek,
- Veriyi tekrar okuyacak,
- DOM'u tekrar oluşturacak.

Bunu yapabilmesinin DOM üzerinde daha hızlı bir yolu yok. **Fakat DOM'u taklit eden bir yapı kullanan** framework, (React, Mercury gibi) bu mümkün olabilir, şöyle ki:

- Eski veri ile yeni veriyi kıyaslayacak (`[1,2,3]` ve  `[1,3,5]`),
- `2`'nin çıktığını ve `5`in eklendiğini anlayacak,
- `2`yi silecek, ve en sona `5`i ekleyecek,
- Değişiklikleri **DOM**'a uygulayacak.

Böylece zaten var olan yapıları silmeden sürekli güncel tutacak. Bu aynı zamanda **performans** demek.

![Image](http://i.imgur.com/VgSS40m.png)

> **Om, Mercury ve Elm'in**  diğerlerine göre daha hızlı olduğu görülüyor.
> Bu projeler **Virtual DOM** yaklaşımı ve **immutable** veri kullanımıyla bu hızlara erişebiliyorlar.

Immutable veri yapıları bambaşka bir performans konusu.

Verdiğimiz "es"i geri alalım ve MVC konusuna geri dönelim.

## Flux

MVC'de verinin akış yolu uygulamanın yapısına göre değişebiliyor. MVC için View, her zaman `render` olması **istenen** bir yapı. Ve bu istekler **controller** tarafından yapılıyor. İstekler arttıkça, render edilmesi gereken yerler fazlalaştıkça uygulama kodu dağılmaya başlıyor.

Bu noktada [Facebook'un Flux'ı][4] anlattığı case olan mesajlaşma uygulamasını inceleyelim.

![Mesajlar](http://i.imgur.com/EzIMpXt.png)

Burayı hızlıca analiz edelim:

  - İki adet chat bölümü var, (**#1, #3**)
  - İkisi de mesajları ortak olarak gösteriyor,
  - Yeni gelen mesaj sayısı gösteriliyor (**#2**)

Bu durumda bu kodu MVC ile yapıyor olsaydık yeni bir mesaj geldiğinde şu olurdu:


{% highlight js %}
getNewMessage: function (message) {
    this.increaseUnreadMessages(); // View #2

    this.addMessageToSmallChat(message); // View #1
    this.addMessageToBigChat(message); // View #3
}
{% endhighlight %}

Bu yöntemde View'ler statik ve sadece bir DOM'a bakan yapılar. Fakat Flux mimarisinde View'lar aynı zamanda geriye de bağlılar.

View'in bir controller'dan veri beklemesi yerine, bir store'u dinlemesi ve store'daki değişikliklere göre davranması çok daha mantıklı. Bu noktada Store'un değişiklikleri bildiren bir yapı olmasını bekleriz.

Eğer mesajları barındıran ortak bir *Store* olsaydı, ve bütün view'lar bu store'da değişiklik olduğunda bu store'daki veriye göre yeniden derlenselerdi hayat daha farklı olurdu. Biz de bu yönteme **Flux** diyoruz.

Biraz daha açalım ve Flux mimarisine bakalım.

### Store

Store adı üzerinde bir veri yapısı. Bir diziyi veya nesneyi store olarak düşünebilirsiniz.

Store ayrıca o veriyi düzenleyen methodlara da sahip olabilir.


{% highlight js %}
var store = Store({
    items: [],
    addAnItem: function () {
        this.items.push({item: 'an item'});
    }
});
{% endhighlight %}

Bu veri yapısını basit bir store olarak düşünebilirsiniz. Store her yaptığı değişiklikten sonra *değiştiğine dair bir sinyal gönderir*.


{% highlight js %}
var store = Store({
    items: [],

    initialize: function () {
        this.on('ADD_ITEM', this.addAnItem);
    },

    addAnItem: function (data) {
        this.items.push({item: data});

        // Veri yapısı değişti
        this.emit('change');
    }
});
{% endhighlight %}

Bu sinyali dinleyen **View** kendisini store'un verisine göre günceller.

### View

Aşağıdaki örnekte View'in store'u dinledikten sonra kendisini güncellemesini gösteren pseudo bir kod mevcut.


{% highlight js %}
var view = View(function () {
    store.on('change', this.render);
});
{% endhighlight %}

View ayrıca son kullanıcıya bakan taraf olduğu için kullanıcıdan gelen "kullanıcı etkileşimlerini" karşılayan yer olma özelliği taşıyor. Aynı store gibi, bu yapılar da **action yayınlarlar**. Böylece bir view'dan sürekli olarak action yayınlanır. Bu actionlar **Dispatcher** adlı yapıda mevcut bulunurlar.

### Dispatcher

Dispatcher yapılar temel olarak kendisine bağlı olan store'lara mesaj gönderen yapılardır. Bu mesajları yukarıda bahsedildiği şekilde View'dan alırlar.


{% highlight js %}
var dispatcher = Dispatcher({

    stores: [store]

    addAnItem: function (data) {
        this.stores.forEach(function (store) {
            store.dispatch('ADD_ITEM', data);
        });
    }
});
{% endhighlight %}

Yukarıdaki view örneğine göre aşağıdaki gibi bir **dispatch** işlemi tanımlayabiliriz.


{% highlight js %}
var view = View(function () {
    store.on('change', this.render);

    this.on('click', function () {
        dispatcher.addAnItem({some: "data"});
    });
});
{% endhighlight %}

### Action Creator

Çoğu aksiyon View üzerinden gelse bile, bazı action'lar view üzerinden gelmez. Mesela **server'dan gelen veri** bir aksiyon olsa da kullanıcı tarafından çekilmez. Bu noktada **Action Creator** devreye girer.

Action creator'ler server veya action'un modifiye edilmesi gereken yerlerde bulunmalıdırlar.


{% highlight js %}
var actionCreator = ActionCreator({

    getItemFromServer: function () {
        $.getJSON('/item/123', function (data) {
            dispatcher.addAnItem({id: data.id});
        });
    }

});
{% endhighlight %}

Daha sonra yukarıdaki örneği değiştirip `actionCreator` üzerinden çağıralım.


{% highlight js %}
var view = View(function () {
    store.on('change', this.render);

    this.on('click', function () {
        // Action creator'den geliyor.
        actionCreator.getItemFromServer();
    });
});
{% endhighlight %}

Action Creator'ler aynı zamanda **initial data** ve **Router'dan** gelen aksiyonlar için de kullanılabilir.

**Flux** aslında bu kadar kolay bir yapıdır.

Aşağıda Flux'ı daha iyi görmeniz için Facebook'un hazırladığı diagramı inceleyebilirsiniz:

![Diagram](https://raw.githubusercontent.com/f/delorean/master/asset/flux-diagram.png)

Flux, şu an arayüz geliştirme üzerinde kullanabileceğiniz en iyi şablonlardan birisi ve gerçek anlamda bir çok sorunu da çözüyor.

---

#### DeLorean.js

Flux'ı kendi uygulamalarınızda rahatça kullanabilmeniz için [**DeLorean.js**][3] isminde bir proje oluşturdum ve oldukça iyi gidiyor. DeLorean'ı [React.js ile rahatlıkla kullanabileceğiniz][3] gibi aynı zamanda diğer frameworkler ile de kullanabilirsiniz. Bir güzel kullanım örneği olarak [Flight.js ile kullanımını][3] da inceleyebilirsiniz.

DeLorean.js'in kullanımı da oldukça kolay.

[1]: https://code.facebook.com/projects/
[2]: https://facebook.github.io/react/
[3]: http://deloreanjs.com
[4]: https://facebook.github.io/flux/
