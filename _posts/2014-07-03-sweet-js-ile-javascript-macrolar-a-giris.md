---
layout: post
title: Sweet.js ile JavaScript Macrolar'a Giriş
abstract: Sweet.js ile JavaScript Macrolar kullanarak kodunuzu modüler hale getirin. Bu yazıda çok ufak bir giriş yapacağız :)
---

Merhabalar,

Bildiğiniz gibi, JavaScript'te bir çok şeyi tekrar ederek aslında aynı kodu sürekli yazıyoruz ve bu çoğu zaman büyük zaman kaybı ve kod karmaşası olarak dönüyor.

    :::javascript
    for (var i = 0; i < 10; i++) {
        console.log(i);
    }

Bu kod üzerinde tekrar ettiğimiz ve aslında *daha kolay* olabilecek bir çok şey var.

    :::javascript
    from 0 to 10 as i {
        console.log(i);
    }

Biraz daha *deklaratif* ve daha okunabilir/düzenlenebilir bir kod.

Hatta biraz daha kısaltarak,

    :::javascript
    to 10 as i {
        console.log(i);
    }

desek hiç de fena olmazdı. Bunun için kullanacağımız araç, Mozilla tarafından geliştirilen [Sweet.js][1].

Sweet.js Macromuzu [macro editor][2] kullanarak düzenleyebiliriz.

Şimdi yukarıda öngördüğümüz makroyu yazalım, ama öncesinde bir makroyu nasıl yazdığımıza bakalım:

    :::javascript
    macro <isim> {
        rule { <kod tasarımı> } => { <çıktı> }
    }

şeklinde bir yapı ile makromuzu tasarlıyoruz. Ve her değişkenimizin başına bir `$` işareti koyuyoruz.

Çağırmak istediğimiz zaman ise

    :::
    <isim> <kod tasarımı>

diyerek makromuzu çağırıyoruz.

    :::javascript
    macro degisken {
        rule { $x } => { var $x; }
    }

Bu makroyu şu şekilde kullandığımızda:

    :::javascript
    degisken x = 10

aşağıdaki sonucu elde edeceğiz:

    :::javascript
    var x = 10;

## Makro Tasarımı

Tabii bu çok basit bir örnek, şimdi yukarıda kurguladığımız `for` makrosunu deneyelim:

    :::javascript
    to 10 as index {
        console.log(index);
    }

Yukarıdaki örneğin aşağıdaki tasarım şablonuna denk geldiğini hissedebilirsiniz:

    :::javascript
    to $value as $index $code

Bu noktada `$value` değeri `10`, `$index` olarak `index` ve `$code` olarak `{ console.log(index); }` vermiş oluyoruz. O halde hemen makromuzu yazalım:

    :::javascript
    macro to {
        rule { $value as $index $code } => {
            ...
        }
    }

`...` yazdığımız yere, bu makronun tam olarak neyi karşıladığını yazalım;

    :::javascript
    macro to {
        rule { $value as $index $code } => {
            for (var $index = 0; $index < $value; $index++) $code
        }
    }

Artık bir makroya sahibiz:

    :::javascript
    to 30 as i {
        console.log(i);
    }

dediğimizde

    :::javascript
    for (var i = 0; i < 30; i++) {
        console.log(i);
    }

şeklinde bir çıktı sağlamış olacağız.

Şimdi bu makroyu biraz daha genişletelim ve yeni bir tasarım daha ekleyelim,

Bu kez tasarımda başlangıç değerini de verelim. Yani

    :::javascript
    to 30 start from 10 as i {
        console.log(i);
    }

yazdığımızda döngü `10`dan başlasın:

    :::javascript
    macro to {
        rule { $value as $index $code } => {
            for (var $index = 0; $index < $value; $index++) $code
        }

        rule { $value start from $from as $index $code } => {
            for (var $index = $from; $index < $value; $index++) $code
        }
    }

Şimdi `to` makrosu iki farklı tasarıma farklı şekilde cevap verebiliyor hale geldi.

    :::javascript
    to 30 start from 10 as i {
        console.log(i);
    }

dediğimizde

    :::javascript
    for (var i = 10; i < 30; i++) {
        console.log(i);
    }

çıktısı alacağız.

## Makroların Birlikte Kullanımı

Sweet.js'in güzel bir tarafı da şu ki, **bir makro diğer bir makroyu kullanabiliyor.**

Şimdi hemen bunu yukarıdaki örneği geliştirerek yapalım:

`to 30 start from 10 as i` gibi bir şablon daha da güzel hale getirilebilir:

    :::javascript
    from 10 to 30 as index

dersek, daha okunabilir bir hale gelecek gibi görünüyor. O halde bir `from` makrosu yazmamız gerekecek.

    :::javascript
    macro from {
        rule { $from to $value as $index $code } => {}
    }

Bu makroyla yukarıdaki şablonu tanımlamış olduk, şimdi bu makro içerisinden `to` makrosunu uyandıralım:

    :::javascript
    macro from {
        rule { $from to $value as $index $code } => {
            to $value start from $from as $index $code
        }
    }

dediğimizde, `from` makrosu, `to` makrosunu da çağırmış olacak.

Bütün olarak aşağıdaki kodu deneyelim:

    :::javascript
    macro from {
        rule {
            $start to $to as $index $block
        } => {
            to $to start from $start as $index $block
        }
    }

    macro to {
        rule {
            $to start from $from as $index $block
        } => {
            for (var $index = $from; $index < $to; $index++) $block
        }
        rule {
            $to as $index $block
        } => {
            for (var $index = 0; $index < $to; $index++) $block
        }
    }


    to 30 as i {
        console.log(i);
    }

    from 10 to 30 as index {
        console.log(index);
    }

    to 10 start from 2 as i {
        console.log(i);
    }

Bu şekilde yeni bir döngü sağlayan makro oluşturmuş olduk.

[Sweet.js][1] çok daha detaylı bir makro kütüphanesi ve bu anlamda detaylıca incelemeniz çok faydalı olacaktır. Makro kullanımlarında **tekrarlar, şablonlar, 'hijyen', `infix`** gibi konuları detaylıca incelemek için [http://sweetjs.org/doc/main/sweet.html][3] adresine göz atmanız son derece faydalı olur :)

Vaktim olursa ikinci kısmını da yazmayı planlıyorum :)

[1]: http://sweetjs.org
[2]: http://sweetjs.org/browser/editor.html
[3]: http://sweetjs.org/doc/main/sweet.html
