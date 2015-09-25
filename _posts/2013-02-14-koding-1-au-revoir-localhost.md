---
layout: post
title: "Koding Vol. 1: Au revoir, localhost!"
abstract: "Geliştiriciler için yepyeni bir platform, Koding: localhost'a \"güle güle\"."
---

Bundan 4, 5 sene önce, Web 2.0'ın çıkmaya başladığı ve kullanıcıların artık internet üzerinde etkileşim kurabilecekleri öngörüldüğü zamanlarda, hatırladığım bir başka öngörü ise Web 3.0'ın **semantik web** ve Web 4.0'ın **internet üzerinde işletim sistemi** yani **WebOS** öngörüsüydü. O zaman bunun ne olduğuna tam anlam verememiştim, çünkü önümüzde pek örnek yoktu ve o kadar da vizyoner bir bakış açısına sahip değildim (hala neler olacağını görmekte zorlanıyorum ve bu işin en heyecanlı tarafı da bu bence). Fakat işin gittiği yönün de bu olduğunu somut örneklerle görmek mümkün. Web 4.0'ın başlangıç noktasını Richard Stallman'ın dediği gibi **tarif etmesi zor** olan *cloud computing*, yani bulut bilişim aldı.

Gelişen yazılım teknolojisi "cloud" dünyasına oldukça alıştı. Kullanıcılar da bu işi epey sevdiler. Kaybolmayan resimler, indirilmeyen müzikler, hızlı internet, kolay bilgi, vesaire. Cloud'un hayatımıza yerleştiğini uzun zamandır *Microsoft Office* veya *iWork* kullanmadığımı farkettiğimde farkettim. Çünkü *Google Docs* güçlü, online, hafif, ücretsiz ve hızlı. Tek sıkıntısı internetinizin olmaması fakat bu artık pek geçerli bir sorun değil.

Artık epostalarımız için bilgisayarımıza bir client kurma gereksinimi hissetmediğimiz, hatta bilgisayarımızda yalnızca bir browser ve internet ile bir çok işi görebildiğimiz bir dönemdeyiz. ChromeOS gibi projeler de bu fikirlerin yansımaları. Gelecek bize neler gösterir bilinmez tabii, fakat geleceğin de bulut bilişimle bütünleştiği bir gerçek. İnternetin somut halde bir varlık olduğu, yoğunlaştığı, bütünleştiği bir dönemdeyiz.

Bu süreçten geliştiriciler de nasibini almaya başlamış durumda. GitHub, geliştiriciler için çok güzel bir örnek olsa da tam anlamıyla bulut sayılmayabilir. Bitbucket ve GitHub üzerinde kod barındırmak, iCloud veya Android Cloud gibi sistemler üzerinde kullanıcıların uygulama verilerini tutmak gibi bir çok amaç cloud'a hizmet ediyor ve internet bulutunu genişletiyor.

Cloud, bu derece gelişmişken o 4, 5 sene önce duyduğum "web üzerinde işletim sistemi" **WebOS** ile ilk tanışmam **[Koding](http://koding.com)** ile oldu. (Gerçekten bu seviyede iddialı bir proje.)

## Koding

Koding'i daha önce Kodingen olarak duymuştum. Muhtemelen bir çok kişi de duymuştur. Şunu belirtmeliyim ki ilk girdiğinizde *bayıldığınız* bir uygulama. Tasarım belli kurallar dahilinde ve standartlara sahip, detaylarına değineceğiz.

![Koding](http://cl.ly/image/1Q2J1U0l0d25/Screen%20Shot%202013-02-14%20at%201.03.04%20AM.png)

Açılışta bir tab sayfası ve menünüzün olduğu, kullanılabilirliğin ve görsel zevkin tepelerde gezdiği bir arayüz ile karşılaşıyorsunuz. Koyu tonlarda olmasını geliştiricilere yönelik olmasına bağladım ben, fakat tamamen zevk için de olabilir. Her ne olursa olsun güzel bir tasarımı var.

Activity feed, aynı Facebook'ta veya Twitter'da olduğu gibi arkadaşlarınızın paylaşımlarını görebildiğiniz bir yer. Activity feed aynı zamanda Koding'e sosyal bir yapı da kazandırıyor. Twitter'da olduğu gibi kişileri takip edebiliyor ve takip edilebiliyorsunuz. Gruplara ve belli konulara abone olup onları takip edebiliyorsunuz.

Koding hesabına sahip olduğunuz zaman `kullaniciadi.koding.com` adında bir alan adına sahip olduğunuzu da ekleyelim. Mesela benim mevcut alan adım `fkadev.koding.com`. Aynı zamanda FTPS kullanarak bu alan adınızdaki dosyalarınıza ulaşabiliyorsunuz. Henüz SSH yok, ama umarım yakında buna da sahip olur.

Geliştiriciler için Develop'a tıklamak yepyeni bir dünyaya kapı aralıyor.

![Develop](http://cl.ly/image/2w1f03012f0J/Screen%20Shot%202013-02-14%20at%201.07.02%20AM.png)

Develop linkine tıkladığınızda yukarıdaki gibi dosya ağacınızı göreceksiniz. Bunlar sizin dosyalarınız. İşletim sistemi dediğimde abartmıyordum, her üyenin bir işletim sistemi, dosyaları ve uygulamaları var.

Koding, içerisinde yükleyebileceğiniz bazı uygulamalarla geliyor. Gökmen Göksel'in geliştirdiği *Wordpress Dashboard* ve *Sample* uygulamaları uygulama geliştirmeye başlamak ve bir uygulamanın neye benzediğini görmek açısından epey faydalı olabilir. Benim geliştirdiğim ve Koding'in store'a koyduğu *Kodepad* ve *Dashboard for GitHub* uygulamalarına, Fatih Arslan'ın geliştirdiği *Django Dashboard* ve *Rails Dashboard* uygulamalarına da Apps altından erişebilirsiniz.

![Terminal](http://cl.ly/image/2u3W0a1r1e1L/Screen%20Shot%202013-02-14%20at%201.12.02%20AM.png)

Bunun gerçek bir terminal olduğunu söylediğimde dalga geçtiğimi veya abarttığımı düşünüyor olabilirsiniz, fakat bu gerçek bir terminal ve belki de Koding'in en vurucu noktalarından birisi. Bir Unix terminali ve tüm Unix komutları burada çalışabiliyor. Arzu ederseniz kendiniz de derleme işlemi yapabiliyorsunuz. C Compiler bile mevcut, daha ne diyeyim. :) Bu arada bu bash prompt benim `.profile` dosyamdaki ayarlardan ötürü bu şekilde, yeni bir hesap açtığınızda standart bash prompt geliyor.

![VIM](http://cl.ly/image/151t0O0E1d1l/Screen%20Shot%202013-02-14%20at%201.14.53%20AM.png)

Bu gerçekten açılmış bir VIM, JavaScript ile taklit edilmiş bir VIM uygulaması değil, VIM uygulamasının kendisini görüyorsunuz. Bunlar gerçekten Koding için çok ufak detaylar.

Geliştiriciler için bu mükemmel arayüzü sağlayan Koding'in yaptığı bir çok şey daha var. Koding üzerinde geliştirme yapıyorsunuz. Kod editörü tüm dosyalarınızı düzenlemenizi sağlıyor. Renk paletlerinizi bile kendiniz değiştirebilirsiniz.

![image](http://cl.ly/image/40460q402d1K/Screen%20Shot%202013-02-14%20at%201.18.14%20AM.png)

Üstelik resimlerinizi düzenlemeniz de mümkün, gördüğünüz bir resim editörü: Pixlr, Koding çalışanı Fatih Acet tarafından geliştirilmiş. Tek bir arayüzde bir çok işlem gerçekleştirebiliyoruz.

![Pixlr](http://cl.ly/image/3W0V3i3H2h0y/Screen%20Shot%202013-02-14%20at%201.21.52%20AM.png)

Koding'in işletim sistemi kadar büyük bir mimari yapısı olduğunu ısrarla vurguluyorum. Birazdan bu konudan bahsedeceğiz zaten. Koding'e kendi uygulamalarınızı da yazabiliyorsunuz ve bunun için tek gereken yine ve sadece *"Koding"*. Ayrıca çoklu tablar ile birden fazla uygulamayı aynı anda da çalıştırmanız mümkün.

Dediğim gibi, Koding'e kendiniz uygulama yazabilirsiniz, ve hatta bu uygulamayı çok ama çok güzel yazarsanız o uygulamayı Koding üzerindeki Koding App store üzerinden diğer kullanıcılarla paylaşmanız da imkansız değil. Benim yazdığım *GitHub Dashboard* uygulaması ile uygulamanızı kullanıcılara yükletebilirsiniz. Yeter ki uygulamanız GitHub üzerinde olsun.

Koding'i yeterince tanıdığımıza göre artık uygulama geliştirme ile ilgili bir şeylerden bahsedebiliriz.

## Koding Temelleri

Koding üzerinde uygulama yazmak için bilmeniz gereken tek şey **CoffeeScript**, **CSS** ve bir nebze **HTML/DOM**. Tabii işletim sistemi tadında uygulamalar için biraz Unix bilmek daha etkili uygulamalar yazmanızı sağlayacaktır. Ayrıca **KDFramework** ve **Kite'lar** hakkında biraz da olsa bilgi edinmek faydalı olabilir.

Öncelikle **Fatih Arslan** tarafından mükemmel şekilde yazılan Koding belgelerine  [http://arslan.koding.com/docs](http://arslan.koding.com/docs) adresinden ulaşmanız mümkün. Burası kılavuzunuz olacak.

### Koding Framework

Koding, KDFramework adında kendi geliştirdikleri bir çatı kullanıyor. Koding'in kendisi bu çatı ile geliştirilmiş ve siz de bu çatıyı kullanarak uygulama geliştirme yapabiliyorsunuz. KDFramework oldukça OOP içeren bir dil, bu yüzden biraz olsun OOP bilmek gerekiyor. Aynı zamanda JavaScript'e ait closure, event-driven yapılara da alışık olmak size hız kazandırabilir.

Koding Framework'te KD ve J sınıfları mevcut. Bu sınıflar arayüzü oluşturmanıza yardım ediyor. KDFramework HTML kullanımını olabildiğince düşürüyor, bu ilk uygulama yazarken biraz zorlasa da (en azından benim alışmam bir nebze zaman almıştı) aslında uygulamanızın Koding standartlarında olabilmesi için büyük önem taşıyor.

Sınıfların listesine yine [http://arslan.koding.com/docs/](http://arslan.koding.com/docs/) adresinden ulaşmanız mümkün. Geliştireceğiniz uygulamalar sırasında buraya sık sık gözatabilirsiniz.

### Koding Kite'ları

Kite'lar, front-end'den işletim sistemine veri gönderen "uçurtmalar". Bana, aynı çocukken gökyüzüne saldığımız uçurtmalarımıza bağladığımız mektupları hatırlatıyor bu isim. "Normalde erişimimizin zor olduğu yerlere bir şeyler göndermek" fikri Koding'in Kite'ları için uygun geliyor ve bu masalsı düşünce uygulama geliştirmeye en azından benim için biraz da olsa "gamification" katıyor. :)

### CoffeeScript

CoffeeScript, Koding'in ana dili. Bu noktada şahsen çok iyi anlaştığımı söyleyebilirim. CoffeeScript, size Ruby/ES6/Python karışımı fakat (kimisi beğenmese de) bana göre oldukça elegant bir syntax sunuyor. Benim standart JavaScript syntax'ı ile hiç bir derdim yok, çok uzun süredir yazdığım için sorun etmiyorum. Fakat CoffeeScript'e alışınca gerçekten işleriniz oldukça kolaylaşıyor.

CoffeeScript'in ne olduğu konusuna değinmem bu yazıyı çok fazla uzatır. Bu yüzden sizi [http://coffeescipt.org](http://coffeescipt.org) adresine yönlendiriyorum.

Bugünlük bu kadar. Bir sonraki bölümde nasıl uygulama geliştireceğimizi göreceğiz. Yani esas bölüm yakında.

Bu arada, Koding kapalı beta konumunda. Dolayısıyla davetiyeniz yoksa sisteme giriş yapamıyorsunuz. Bende birkaç davetiye var, ilginizi çekerse davetiye gönderebilirim.

**to be continued…**
