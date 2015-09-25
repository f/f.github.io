---
layout: post
title: "Fonksiyonel Programlama Notları 3: Saf Fonksiyonel Promise Monad"
abstract: Fonksiyonel Programlama ve Monad serüveni beni daha önce de anlattığım promise monad'ını tekrar yazmaya itti.
---

Sevgiler efendim. Uzun zamandır Fonksiyonel Programlamayı sökmeye çalışıyorum. Bu noktada karşıma yepyeni zorluklar ve zevkli işler çıkıyor. Nihayet Monad'ları biraz anlamayı başarabildim. Ve JavaScript'te daha önce yazdığım (monad olduğunu sonradan öğrendiğim) **Promise Monadı'nı**'i tekrar yazayım istedim.

Daha önceden yazdığım Monad fonksiyonel programlamanın gerekliliklerini yerine getirmiyordu. Fakat bu implementasyonumda olabildiğince o düzeyde durmaya çalıştım. Umarım daha faydalı olmuştur.

## Promise Monadı

Daha önce de dediğimiz gibi, fonksiyonel programlamada işleri *olabildiğince küçük ve saf fonksiyonlar* haline getirip bu fonksiyonları birbiriyle **compose** ederek yeni fonksiyonlar elde ediyorduk, ve eğer bu fonksiyonlar saf halini bozmadan, recursion vs. gibi yöntemlerle bir şekilde state taşıyorlarsa bu fonksiyonlara **Monad** diyorduk. Bu tanımda hata varsa lütfen düzeltin.

Şimdi **Promise** algoritmasının fonksiyonel olarak nasıl implemente edildiğine bakalım.

Kod üzerine açıklama satırı yazarak anlatmaya çalıştım;

    :::javascript
    // `successes` ve `fails` parametreleri aslında durumu taşımaya yardımcı olan **monadic** parametreler.
    function Promise(onsuccess, onfail, successes, fails) {

      // Daha sonra bu state taşıyan parametrelere varsayılan değerlerini verelim;
      if (!successes) successes = [];
      if (!fails)     fails = [];

      // Eğer bir success değeri geldiyse bunu da ayarlayalım:
      if (onsuccess)  successes.push(onsuccess);

      // .. tabii fail değerini de ayarlamalı...
      if (onfail)     fails.push(onfail);

      // Aynı değerlerden yeni bir promise oluşturalım;
      // Safi olması için `this` gibi nesnel değerleri kullanmıyorum.
      function staticPromise() {
        return Promise(false, false, successes, fails);
      }

      // resolve fonksiyonu context parametresi alıyor ve bu sayede
      // tüm resolve fonksiyonlarını nasıl bir context ile çalıştıracağını
      // belirliyor.
      function resolve(context) {

        if (!successes.length) {
          return staticPromise();
        }

        successes.shift().apply(context, Array.prototype.slice.call(arguments).slice(1));
        return staticPromise().resolve();

      }

      // reject fonksiyonu resolve'un aynısı, reject için çalışıyor.
      // hatta belki kısaltılabilir bile..
      function reject(context) {

        if (!fails.length) {
          return staticPromise();
        }

        fails.shift().apply(context, Array.prototype.slice.call(arguments).slice(1));
        return staticPromise().reject();
      }

      // success fonksiyonu bir fonksiyonu onsuccess olarak belirleyen ve mevcut durumu taşıyan yeni bir promise dönüyor.
      function success(fn) {
        return Promise(fn, false, successes, fails);
      }

      // fail fonksiyonu bir fonksiyonu onfail olarak belirleyen ve mevcut durumu taşıyan yeni bir promise dönüyor.
      function fail(fn) {
        return Promise(false, fn, successes, fails);
      }

      // then ise her iki parametreyi de belirleyip yeni bir promise dönüyor.
      function then(fn) {
        return Promise(fn, fn, successes, fails);
      }

      return {
        then    : then,
        success : success,
        fail    : fail,
        resolve : resolve,
        reject  : reject
      };

    }

## When Monadı

Daha önceki yazımda when algoritmasını daha uzun yazmıştım; fakat fonksiyonel programlama ile daha hızlı bir sonuç elde edebiliyorsunuz.

When de aslında bir *Promise*. Yani implemente ederken promise kullanıyorsunuz.

`When` de bir monad. Yine state taşıma özelliğine sahip ve saf sayılabilir. (`deferred.reject` kısmı biraz yanetki gibi ama orası için daha farklı çözümler arıyorum.)

    :::javascript
    // İki farklı state taşıyor, birisi bütün promiseler bir diğeri ise deferred.
    function When(promises, deferred) {
      // deferred varsayılan bir promise ve bu state taşınacak.
      if (!deferred) deferred = Promise();
      if (!promises) promises = [];

      // Eğer promise varsa..
      if (promises.length > 0) {
        // .. İlkini al ve success değerini ata..
        promises.shift().success(function () {
          // .. bir eksiltilmiş olan promise dizisinin yeni halini tekrar monad'a
          // gönder..
          When(promises, deferred);
        }).fail(function () {
          // .. ve herhangi bir fail durumu işlemi reject eder..
          deferred.reject();
        });
      } else {
        // .. ve yine hiç bir promise kalmadıysa, çözülmüştür.
        deferred.resolve();
      }

      // deferred'i döndür..
      return deferred;
    }

    // bu da dizi yerine parametre verebileceğiniz bir yardımcı..
    function when() {
      return When(Array.prototype.slice.call(arguments));
    }

Şimdi testlerimizi yapalım:

    :::javascript

    x = Promise(function () {
      console.log("x resolved");
    })

    x.success(function () {
      console.log("x resolved again..");
    });

    x.success(function () {
      console.log("x resolved again and again..");
    });

    y = Promise().success(function () {
      console.log("y resolved");
    });

    y.fail(function () {
      console.log("y rejected");
    });

    When([x, y]).success(function () {
      console.log("x and y all resolved");
    });

.. ve çalıştıralım:

    :::javascript
    x.resolve(); //=> "x resolved", "x resolved again..", "x resolved again and again.."

    y.resolve(); //=> "y resolved", "x and y all resolved"

Önce `y`yi daha sonra `x`i resolve etmiş olsaydık yine aynı durum oluşacaktı. Eğer herhangi bir rejection işlemi yapsaydık;

    :::javascript
    When([x, y]).success(function () {
      console.log("x and y all resolved");
    }).fail(function () {
      console.log("something rejected!");
    });

    y.reject(); //=> "something rejected!"

Monad'lara hala çalışıyorum; eğer hata yaptıysam mutlaka düzeltmiş olurum :)    

## Yorumsuz Kod

Yorumlar kodu ilk bakışta görmeyi zorlaştırıyor gibi geldi; bu yüzden kodu bir de yorumsuz ekliyorum:

    :::javascript
    function Promise(onsuccess, onfail, successes, fails) {

      if (!successes) successes = [];
      if (!fails)     fails = [];

      if (onsuccess)  successes.push(onsuccess);
      if (onfail)     fails.push(onfail);

      function staticPromise() {
        return Promise(false, false, successes, fails);
      }

      function resolve(context) {

        if (!successes.length) {
          return staticPromise();
        }

        successes.shift().apply(context, Array.prototype.slice.call(arguments).slice(1));
        return staticPromise().resolve();

      }

      function reject(context) {

        if (!fails.length) {
          return staticPromise();
        }

        fails.shift().apply(context, Array.prototype.slice.call(arguments).slice(1));
        return staticPromise().reject();
      }

      function success(fn) {
        return Promise(fn, false, successes, fails);
      }

      function fail(fn) {
        return Promise(false, fn, successes, fails);
      }

      function then(fn) {
        return Promise(fn, fn, successes, fails);
      }

      return {
        then    : success,
        success : success,
        fail    : fail,
        resolve : resolve,
        reject  : reject
      };

    }

    function When(promises, deferred) {

      if (!deferred) deferred = Promise();
      if (!promises) promises = [];

      if (promises.length > 0) {
        promises.shift().success(function () {
          When(promises, deferred);
        }).fail(function () {
          deferred.reject();
        });
      } else {
        deferred.resolve();
      }

      return deferred;
    }

    function when() {
      return When(Array.prototype.slice.call(arguments));
    }        
