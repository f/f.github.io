---
layout: post
title: Test Driven Development
abstract: "Size kısaca bir gereklilik olan fakat genellikle hiç kullanmadığımız bir konu hakkında biraz temel bilgi vermek istiyorum: Test Driven Development. Türkçe'ye \"Test Güdümlü Geliştirme\" olarak çevrilmiş sanırım. Her neyse, konuya gelelim..."
---

Merhaba,

Size kısaca bir gereklilik olan fakat genellikle hiç kullanmadığımız bir konu hakkında biraz temel bilgi vermek istiyorum: Test Driven Development

Türkçe’ye "Test Güdümlü Geliştirme" olarak çevrilmiş.

## Niçin test yazmamız gerekiyor?

Geleneksel kodlamada işler şu şekildedir;

 * Sınıfı (sınıf bile olmayabilir) yaz,
 * Kodu deploy et,
 * Hata bildirilirse hatayı bul ve kodu kendin düzelt.

Biraz daha geliştirilmiş geleneksel kodlamada ise işler şu şekildedir;

 * Sınıfı yaz,
 * Olası case’ler içine loglar koy,
 * Kodu deploy et
 * Hata bildirilirse loglara bak ve kodu düzelt.

Fakat bu geleneksel yöntemler nesne yönelimli programlama sırasında biraz canınızı sıkabilir, çünkü bu kez loglamaları
koymanız gereken yer sizin sınıflarınız olacak ve her ne kadar kod tekrarını engellemiş olsanız da, tekrar kullanılabilirlik
o derece aşağılara düşecektir. Yani aslında yaptığınız şey OOP değil OOP görünümlü bir yapı olacaktır. Hem stabilizasyonu
artırmak hem de daha hızlı ve düzgün kod yazmak için kullanılması gereken yöntem ise **TDD**’dir.

İnternette TDD ile ilgili bulduğunuz dökümanları aradıktan sonra kendinize şu soruyu soruyorsunuz genellikle;
"Niçin test yazayım ki, ne gereği var?" ve ardından; "Test yazacağıma kod yazarım, test yazmakla vakit harcamaktansa.."
düşünceleri kafanızda dönüyor. Fakat işler tam düşündüğünüz gibi değil.

## Örnek

Şimdi biraz örnekle inceleyelim; diyelim ki bir matematik sınıfımız var.
Klasik dört işlemi yapabilsin, yani toplama, çıkarma, çarpma, bölme işlemleri için birer method olsun. Normalde nasıl bir sınıf yazardık?

    :::php
    <?php
    class Math {

	    /**
	     * Toplama işlemi
	     *
	     * @param  $a
	     * @param  $b
	     * @return int Sonuç
	     */
	    public function add($a, $b)
	    {
		    return $a+$b;
	    }

	    /**
	     * Çıkarma işlemi
	     *
	     * @param  $a
	     * @param  $b
	     * @return int Sonuç
	     */
	    public function subst($a, $b)
	    {
		    return $a-$b;
	    }

	    /**
	     * Bölme işlemi
	     *
	     * @param  $a
	     * @param  $b
	     * @return int Sonuç
	     */
	    public function div($a, $b)
	    {
		    return $a/$b;
	    }

	    /**
	     * Çarpma işlemi
	     *
	     * @param  $a
	     * @param  $b
	     * @return int Sonuç
	     */
	    public function multiple($a, $b)
	    {
		    return $a*$b;
	    }

    }

Veya daha zekice işlemler, daha farklı implementasyonlar olabilir. TDD sizi kod yazmayı severken yazdığınız kod kadar iyi kod yazmaya yönlendiriyor.

## İlk olarak test yazılmalı

Nasıl yani? Olmayan bir sınıfı mı test edeceksiniz? _Evet, aynen öyle_. Çünkü TDD testten geçmeniz için var olan
eksiklerinizi söyleyecek, sınıf yazılması da dahil. Hani okulda sınavdan geçemediğimiz zaman öğretmenlerden hangi
soruda hata yaptığımızı öğrenmek isterdik ya, diğer sınavı geçebilmek adına, mantık tamamen aynı işliyor :)

## Test Sınıfı ve PHPUnit

Not: Burada sisteminize **PHPUnit** yüklediğinizi varsayıyorum.

Önce bomboş bir `Math.php` oluşturun, daha sonra ise bir örnekteki gibi bir `MathTest.php`

    :::php
    <?php
    require_once 'Math.php';

    class MathTest extends PHPUnit_Framework_TestCase {
	    /**
	     * @var Math
	     */
	    protected $object;

	    protected function setUp() {
		    $this->object = new Math;
	    }

	    protected function tearDown() {
	    }
    }

Örnekte gördüğünüz gibi `PHPUnit_Framework_TestCase` sınıfını extend eden bir `MathTest` sınıfı yazdık. daha sonra buna bir `setUp` bir de `tearDown` methodu yazdık.

`setUp` methodu, her test başlangıcında yapılacakları; `tearDown` ise bitişte yapılacakları içerir. Yani her test case’inden önce `Math` objesi yeniden oluşturulacak ve `MathTest`
objesinin object propertysini sıfırlayacak. Güzel. Zaten burada genelde pek bişey yapmazsınız. Devam edelim.

Testi hala çalıştırmıyoruz, şimdi biraz test yazalım:

    :::php
    <?php
    require_once 'Math.php';
    class MathTest extends PHPUnit_Framework_TestCase {
	    /**
	     * @var Math
	     */
	    protected $object;

	    protected function setUp() {
		    $this->object = new Math;
	    }

	    protected function tearDown() {
	    }

	    public function testTamSayiToplama() {
		    $this->setName('Tam sayıların toplamı');

		    $this->assertEquals(3, $this->object->add(1, 2), '1+2 = 3');
		    $this->assertEquals(5, $this->object->add(2, 3), '2+3 = 5');

	    }

	    public function testNegatifSayilarinToplami() {
		    $this->setName('Negatif sayıların toplamı');

		    $this->assertEquals(-4, $this->object->add(-2, -2), '-2+(-2)=-4');
		    $this->assertEquals(-4, $this->object->add(10, -14), '10+(-14)=-4');
	    }
    }

Gördünüz mü, zor değil, sadece `test` ile başlayan ve istediğimiz bir method adını yazıyoruz, daha sonra içerisine methoddan ne beklediğimizi yazıyoruz:

**Ben sana şu parametreleri verdim, senden beklediğim ise şu;...**

diyerek testimizi yazıyoruz. Burada gördüğünüz gibi toplama işlemlerimizin bize nasıl cevaplar vermesi gerektiğini önceden hazırlıyoruz, ve tüm her case’i
çalışıyormuş gibi yazdıktan sonra classımız “All tests passed” diyinceye kadar çalıştırıyoruz. Ve yazdığınız bu tüm caselerden geçen sınıf en düzgün çalışan
kod oluyor. Bu kadar basit. Eğer sonradan sınıfınız bir case’de çalışmazsa, suçu yazdığınız sınıfa değil teste atıyorsunuz; demek ki testi yanlış
yazdınız ve bir case’i düşünmediniz.

Sonuç olarak bizim asıl beklentimiz sınıfın çalışan sistem üzerinde nasıl çalıştığı değil; testlere nasıl cevap verdiğidir.

Son bir örnekle devam edelim ve bitirelim; demin yazmış olduğum test sınıfından geçen sınıf şuydu:

    :::php
    <?php
    class Math {

      /**
       * Toplama işlemi
       *
       * @param  $a
       * @param  $b
       * @return int Sonuç
       */
      public function add($a, $b)
      {
	      return $a+$b;
      }
    }

Fakat testler sırasında bir olayı düşünemedik, peki ya şöyle bir case olsaydı?

    :::php
    <?php
    //...
    $this->assertEquals(26, $this->object->add(5,3,2,1,5,10), '5+3+2+1+5+10=26 olmalı');
    //...

O zaman demin yazdığımız methodun implementasyonu tamamen hatalıydı. Çünkü iki parametre alıyoruz ve yalnızca aldığımız iki parametreyi topluyoruz, o halde testten geçmesi için;

    :::php
    <?php
    //...
	    public function add(/* sınırsız değer alabilir... */)
	    {
		    //fonksiyona gelen parametrelerin tümünü array olarak alalım.
		    $values = func_get_args();
		    //gelen arrayın değerlerini toplayarak geri dönelim
		    return array_sum($values);
	    }
    //...

şeklinde görüldüğü üzere, `add` methodumuzun implementasyonunu bu testi geçecek şekilde düzenliyoruz.

Son örnekte de gördüğünüz gibi, testler kodunuzu düzgün yazmanıza yardım eder ve aslında gereksiz değildir. Hatasız ve düzgün bir şekilde kodunuzu yazarsınız.

Benden temel olarak bu kadar, dahasını ve detaylı bilgileri PHPUnit veya hangi programlama dilini kullanıyorsanız onun UnitTest frameworkünü çalışarak öğrenebilirsiniz.

Kolay gelsin :)
