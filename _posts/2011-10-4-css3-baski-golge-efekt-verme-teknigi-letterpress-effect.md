---
layout: post
title: "CSS3 ile Yazıya Baskı Gölge Vermek - 1: Tekniğin Detayları (Letterpress Effect)"
abstract: Yazılara verilen baskı efekt tekniğinin açıklaması
---

<style>
  iframe {
    width: 550px;
    height: 300px;
  }
</style>

Merhaba,

Bugün değinmek istediğim konu bu aralar çok sık gördüğümüz bir efekt, letterpress efekti. Harf baskısı efekti diye
de çevirebiliriz. Bu yazımda size hazır efekt vermek yerine, bu efekti kendiniz nasıl yapabilirsiniz bu konudan
bahsetmek istiyorum. Balık tutmayı öğretmeyi düşünüyorum bir nevi. :)

Fiziksel dünyada bir kağıda üzerinde mürekkep olmayan bir damga ile basarsanız, damga üzerindeki metin kağıt üzerinde
baskı oluşturur, okunabilir hale getirir. Aslında o yazının okunabilir hale gelmesinin sebebi tamamen ışıktır.
Yani gölge sayesinde yazıyı okuyabilir hale gelirsiniz.

## Metin rengi, arkaplan renginden biraz koyu olmalı.

Yani bu efekti sağlamak için aynı bu şekilde düşünmemiz gerekir. Şimdi biraz fiziksel düşünelim. Bir kağıda baskı yaptığımızda,
mürekkep olmadan dahi baskı ışığından metni okuyabiliyorsak, bizim de CSS üzerinde bir tür mürekkep kullanılmamış efekti
sağlamış olmamız gerekiyor. Yani baskı yaptığımız kağıdın rengi ne ise (`background-color`) baskı metninin rengi de (`color`)
ona yakın olmalı. Fakat baskı yaptığımız için, kağıdın kendisi bu çukur kısma gölge düşürecektir, yani `color` değeri
`background-color` değerinden biraz koyu olmalı. Eğer açık renkte olursa bu fiziki gerçekliğe uymaz, beynimiz de bunu
algılamaz doğal olarak.

## Işık yönü, gölge ve parlayan kısımlar

Işığı genellikle sol üst köşeden geldiğini düşünerek hareket ederiz. Yaptığınız tasarımda genel gölge eğilimi ne tarafa ise,
bu gölge de o tarafa olmalı. Bu bizim için önemli değil, fakat bizim için önemli olan nokta şu, baskı efekti yukarıdan ışık
alırsa, normal düşen gölgeden farklı olarak iki adet gölge oluşur. Aslında bunlardan **birisi gölge, diğeri ise parlayan kısım.**

Mantık olarak, parlayan kısmın çok açık bir renkte olması gerekir, ışık orayı doğrudan görüyordur. Gölge ise parlaklıktan
en az iki ton daha koyu olması gerekir. İlk ton, yüzeyin rengi, ikinci ton baskı içerisindeki gölge, üçüncü ton ışığı hiç
görmeyen gölgenin rengi, ve dördüncü ton ise en parlak kısmın rengi. Yani temelde dört ton renk ortaya çıkarmış olduk,
bunu biraz formülize edelim:

  * Yüzeyin rengi (X birim ton)
  * Baskı metninin rengi (X+2 birim ton, yüzey renginden biraz koyu)
  * Parlak kısmın rengi (X-2 birim ton, baskı metninden çok daha açık, yüzey renginden biraz açık)
  * Koyu kısmın rengi (X+4 birim ton, yüzey renginden daha koyu)

<iframe src="http://jsfiddle.net/yhE7D/1/embedded/css,result"></iframe>



Bu değerler tamamen örnek değerler tabii, siz biraz ince ayar çekebilirsiniz :) Gördüğünüz gibi basit bir şekilde efekti yaratmanız
mümkün. Bunu her renk için uygulayabilirsiniz, aşağıda küçük bir örnek yaptım, onu da inceleyebilirsiniz. Örnek içerisinde koyu
ve açık renkte iki adet örnek var.

<iframe src="http://jsfiddle.net/ZxSA3/6/embedded/css,result"></iframe>

Sonuç:

![Letterpress](http://f.cl.ly/items/191H1P3w1v0D34183P2F/Screen%20Shot%202011-09-12%20at%2018.18.02.png)

Burada gradyan efektleri de kullandık, biraz ince ayar çekilmiş durumda. Fakat yukarıdaki örnekle bu tarz efektler yaratmanız mümkün.

Umarım işinize yarar, iyi günler :)
