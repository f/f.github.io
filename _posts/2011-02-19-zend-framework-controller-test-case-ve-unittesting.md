---
layout: post
title: Zend Framework Controller Test Case ve UnitTesting
abstract: Zend Framework'te Controller sınıflarının testleri nasıl yapılır?
---

## Test Dizini

Nasıl yani ne alaka, her yerde yazıyor diyebilirsiniz fakat gerçekten bir kaynağa ulaşmak bu kadar mı zor olurdu?
Sanki herkesin bildiği bir şeyi kimse yazmamış, gizli bir belgeymiş gibi...

Tabii en sonunda olayı çözdüm.

Bildiğiniz gibi

    zf create project

dediğinizde `tests` diye bir dizininiz oluyor. ve bir de test dizini altında boş bir `application/bootstrap.php`.
Aslında bu dosya tamamen opsiyonel, ben işlemlerimi bootstrap.php yerine setUp methodu altında yapmayı, hatta
sürekli aynı ayarları yapmamak için abstract bir sınıf oluşturmayı tercih ediyorum. Zor şartlardan sonra oluşturduğum
sınıf aşağıdaki şekilde:

    :::php
    <?php
    require_once 'Zend/Loader/Autoloader.php';
    Zend_Loader_Autoloader::getInstance();

    abstract class ControllerTester extends Zend_Test_PHPUnit_ControllerTestCase {
      /**
       * Application Path
       *
       * @var string Uygulama Yolu
       */
      private $application_path = NULL;
      private $include_path_appended = false;

      protected function setUp() {
        //application path'i ayarlıyoruz
        $this->_initApplicationPath();

        //include path'i ayarlıyoruz
        $this->_initIncludePath();

        //yeni zend uygulaması başlatıyoruz
        $this->bootstrap = new Zend_Application(
	        'testing',
		        APPLICATION_PATH . '/configs/application.ini'
        );
        parent::setUp();
      }

      /**
       * Uygulama yolunu verir, APPLICATION_PATH testlerin düzgün çalışması için gereklidir.
       *
       * @return void
       */
      private function _initApplicationPath() {
        //eğer zaten mevcutsa çıkıyoruz.
        if (!is_null($this->application_path))
	        return;

        $this->application_path = realpath(dirname(__FILE__) . '/../../application');

        defined('APPLICATION_PATH') || define('APPLICATION_PATH', $this->application_path);
      }

      /**
       * Library dizinini include_path'e ekler.
       *
       * @return void
       */
      private function _initIncludePath() {
        //eğer zaten append edildiyse çıkıyoruz.
        if (!$this->include_path_appended)
	        return;

        set_include_path(implode(PATH_SEPARATOR, array(
	        realpath(APPLICATION_PATH . '/../library'),
	        get_include_path(),
        )));

        //include path'in append olduğunu bildiriyoruz.
        $this->include_path_appended = true;
      }

      protected function tearDown() {
      }
    }

Bu abstract sınıfımızı `/tests/application` altına gönderdikten sonra artık işlemlerimiz daha kolay bir hal almaya başlıyor.

`IndexControllerTest.php`’mizi oluşturup:

    :::php
    <?php
    include 'ControllerTester.php';

    class IndexControllerTest extends ControllerTester {

	    public function testAsd() {
		    $this->dispatch('/test/22');
		    $this->assertQueryContentContains('div#adi', 'fatih kadir akin=22', 'ID 22 olmalı.');
		    $this->resetRequest()->resetResponse();

		    $this->dispatch('/test/23');
		    $this->assertQueryContentContains('div#adi', 'fatih kadir akin=23', 'ID 23 olmalı.');
	    }

    }

dedikten sonra, artık PHPUnit üzerinden çalıştırabilir hale gelmiş oluyoruz.

İşte bu kadar, ilk verdiğim sınıfı inceleyerek biraz fikir edinebilirsiniz. Tüm olay `$this->bootstrap` kısmında bitiyor.

Kolay gelsin :)
