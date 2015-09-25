---
layout: post
title: "Object Oriented Javascript'e Giriş: Fonksiyonlar"
abstract: "Nesne Yönelimli JavaScript'e giriş Dersleri, 1. Bölüm: Fonksiyonlar"
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Bu blog girdisini aslında daha farklı şekilde yazmayı planlıyordum fakat, vaktimin yetersizliği vs. derken buraya yazmaya karar verdim.

Beni tanıyanlar Javascript ile olan bağımı da bilirler :) Javascript, modern Web’in vazgeçilmez unsurudur. Ne zaman ki insanlar interneti
browser dışında yeni bir oluşumla gezinmeye başlarlar, ne zaman ki browserlar farklı bir dinamik dil kullanmaya başlarsa o zaman Javascript
bitecek demektir. Şimdilik bu ufuk görünürlerde değil. :)

Javascript, -bir çok kişinin düşündüğünün aksine- zor bir dil değildir. Yine -bir çok kişinin düşündüğünün aksine- çok basit bir dil de değildir.
Basit olmasının inancı çok hızlı sonuç alabiliyor olmamızdandır. Zor olduğu inancı ise gittikçe derinleşen bilgi gereksinimleri.
Basit bir `alert` fonksiyonu, sizin bazı ihtiyaçlarınızı karşılayabilecek niteliktedir.

Gelelim konumuza;

Javascript’in tarihine vs. girecek değilim. Zira, Google’da arattığınızda sayfalarca metin bulursunuz. **Konuyu direk Fonksiyonlar‘dan
almamın sebebi, temel JavaScript bilginiz olduğunu düşünmemdendir. Değişkenleri anlatmadan giriyorum konuya, eğer bu konuda da bilginiz
yoksa, basit bir kaç kaynaktan araştırabilirsiniz.**

Diğer programlama dillerinde bir kavram vardır: **Sınıflar**. Bu kavram JavaScript’te geçerli olmamakla birlikte, diğer dillerde var olan bu
yeteneği fazlasıyla kullanırlar. JavaScript’te her fonksiyon aslında birer sınıftır. Fakat aynı zamanda birer objedir, çünkü fonksiyonlar
aslında Function fonksiyonunun birer objeleridir. Karışık gelmiş olabilir, şöyle özetleyelim: “her fonksiyon aslında `Function` fonksiyonunun
bir objesidir, ve kendisinden yeniden obje oluşturulabilir”

<iframe src="http://jsfiddle.net/fkadev/TUAWL/embedded/"></iframe>

Yukarıda da küçük bir ispatı zaten mevcut :)

Javascript’te bir fonksiyon türlü şekillerde tanımlanabilir. Bu Javascript’in esnekliğinin yine bir ispatıdır.

<iframe src="http://jsfiddle.net/fkadev/xXqTA/embedded/"></iframe>

Bu şekilde aslında isimsiz bir fonksiyon oluşturmuş oluyoruz. Ve bu fonksiyonu bir değişkene atıyoruz. Bu avantajlı olduğu kadar,
bazı implementasyonlarda sıkıntı yaratmakta. Mesela fonksiyonun ismi olmadığından fonksiyonun kendi ismini alması gerektiği durumlarda
almak zor oluyor (fonksiyon parsing vesaire). Fonksiyonun kendi ismini alması durumundan daha sonra bahsedeceğiz (`arguments.callee`).
Ayrıca görüldüğü gibi, sonuna `};` koyduk. Bunun sebebi, aslında `var a = "b";` gibi bir işlem yapmamış olmamızdan. _Bu yumuşak bir kural,
kodunuzun çalışmasına engel teşkil etmez, fakat olası hatalardan kaçınmak adına bu şekilde kullanmakta yarar vardır._

Aşağıdaki örnekte göreceğiniz gibi, isimli fonksiyonları yine isimli başka bir değişkene atayabilirsiniz.

<iframe src="http://jsfiddle.net/fkadev/SXnzw/embedded/"></iframe>

Gördüğünüz gibi, fonksiyon isimsiz bir fonksiyon gibi oluştu. Fakat yalnızca kendi bildiği bir ismi var. **Anonim fonksiyonlara isim vermek,
anonim fonksiyonun kendi ismine ihtiyacı olduğu noktalarda yararlı olabilir.** Aslında başka zaman yararlı olmaz. Bu yüzden bu konuya da fazla
takılmayın, fakat Javascript’in esnekliği konusunda emin olun :)

Javascript’te `string` tabanlı fonksiyon oluşturmak da mümkün.

<iframe src="http://jsfiddle.net/fkadev/MJcyn/embedded/"></iframe>

Bu fonksiyon da tamamen isimsiz bir fonksiyon oluşturur.

Javascript’te fonksiyonlar bu kadar. “Bu kısımla ilgili bilgimiz zaten vardı” diyen arkadaşlar için Prototyping konusu ikinci bölümde. :)
