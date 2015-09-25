---
layout: post
title: "Object Oriented Javascript'e Giriş: Prototype"
abstract: "Nesne Yönelimli JavaScript'e giriş Dersleri, 2. Bölüm: Prototype"
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Merhaba,

Bu yazı dizisinin bir önceki postunda bu kez prototyping ile ilgili bir metin yayınlayacağımı söylemiştim.
Bu yazılarda genel olarak teori yerine detaylı uygulama ile anlatmayı tercih edeceğim. Eğer işin teori
kısmını merak ediyorsanız ECMAScript spesifikasyonlarından faydalanabilirsiniz.

Çok uzatmadan konuya geçelim diyorum;

Javascript’te **sınıf** kavramının, diğer dillerdeki gibi olmadığına dikkat çekmiştik. JavaScript, prototype
tabanlı nesnel bir dildir. Her fonksiyon bir sınıftır. Ve fonksiyonun kendisi, bu sınıfın `constructor`‘udur.

Bu yazıya/yazı dizisine, kendi bireysel çalışmalarımda da en çok kullandığım `Animal > Bird > Parrot`
sınıf ilişkisiyle devam edelim diyorum :)

Bir sınıf, fonksiyonun kendisidir demiştik. Yani Javascript’te fonksiyon yazdıysanız, aslında bir sınıf yazmışsınızdır.

_Peki_, JavaScript’te her fonksiyon bir sınıf ise, fonksiyonu çağırdığımızda bize niçin nesne dönmüyor, fonksiyonun return değerini dönüyor?

Sorunun cevabı yine Javascript’in esnekliğinde. Eğer siz fonksiyonu sınıf olarak kullanmak istiyorsanız sınıf,
method olarak kullanmak istiyorsanız method olur. Fakat, sınıf olduğu durumlarda constructor’un `return` değeri hiç bir anlam
ifade etmiyor. **Bu da bize yeni bir esnek alan sağlıyor, ve biz de bundan yararlanıyoruz.** Fakat bu konuya daha sonra değineceğiz.

O halde laftan çok iş yapalım ve ilk örnek fonksiyonumuzu yazalım:

<iframe src="http://jsfiddle.net/fkadev/quAgA/embedded/"></iframe>

Gördünüz mü :) `new` keywordünü eklediğimizde değişken bir anda nesneye dönüverdi. Çünkü `Animal` fonksiyonu, aslında bir sınıf,
ve aslında bir method (fonksiyon). Ne kadar güzel :)

Yukarıdaki fonksiyonda `return ":)";` kullandık. Demek ki fonksiyon method olarak çağırıldığında, istediğimiz değeri dönebiliyoruz.
Bu kısma da değineceğiz. _Singleton design pattern_ yapılarında bu özellikten yararlanmaya çalışacağız.

Peki ya nesne kendisine ulaşmak istiyorsa? Bu durumda this keywordü işimize yarayacak. Biraz kafası karışmış olabilecek arkadaşlar
için detaylandırıyorum; bir nesnenin kendisine ulaşması demek, bir kuşun kanadını kendi başına çırpma yeteneğine sahip olması demektir.
Dışarıdan bir güdüm olmadan kendi kanadını kontrol edebiliyorsa, o kuş `this` sahibidir. En basit bu şekilde anlatılabiliyordu. :)

<iframe src="http://jsfiddle.net/fkadev/grP9k/embedded/"></iframe>

Gördüğümüz gibi this objesi ile `Animal`’dan türetilmiş nesnenin kendisine eriştik. İşte şimdi `prototype`‘lara giriş yaptık.
`this` nesnesi, aslında `Animal.prototype`’ın ta kendisi, ve o nesnedeki yansımasıdır.

Prototyping - 2 başlıklı yazıyı da en kısa sürede yayınlamayı düşünüyorum, bugünlük bu kadar. Umarım beğeniyorsunuzdur :)
