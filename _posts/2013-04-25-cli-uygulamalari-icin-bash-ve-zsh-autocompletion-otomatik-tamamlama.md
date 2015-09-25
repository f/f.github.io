---
layout: post
title: CLI Uygulamaları için bash ve zsh Autocompletion (Otomatik Tamamlama)
---

Merhaba,

Front-end development'in en eski türlerinden biri olan CLI development'tir. Yani **Command Line Interface**. Her ne kadar HTML veya masaüstü arayüzleri gibi olmasa da o da en nihayetinde bir interface'dir. Bir **front-end**'dir.

Bash'de autocompletion'u bilirsiniz. TAB karakterine basarak otomatik tamamlama yapmanızı sağlayan özelliktir.

    $ sudo apt-<tab>

dediğinizde

    $ sudo apt-<tab>
    apt-get apt-add-repository

şeklinde tamamlamalarda bulunarak sizin bash üzerinde daha hızlı komut yazmanıza yardımcı olur.

Yazdığınız bir komuta bash autocompletion desteği vermek de bir o kadar basit.

Örnek üzerinden anlatırsak daha faydalı ve öğretici olacağını düşünüyorum. Adı `fka` olan bir program yazmak istiyoruz diyelim. Öncelikle CLI uygulamamızın kısımlarını belirlememiz gerekiyor.

    $ fka [modul] [komut] [opsiyon] [--opsiyon]

Temelde komutun kendisinden sonra 4 farklı komut aldığını gördük. Bunu şimdilik sadece 2 komut alacak şekilde ayarlayalım.

    $ fka [modul] [komut]

Otomatik tamamlamak istediğimiz kısımlar bunlar olsun

    $ fka blog new-post

şeklinde bir CLI front-end'i ile yeni bir blog metni yazabileceğimizi düşünelim.

    $ fka <tab>
    blog ftp site

gibi bir otomatik tamamlama olacak. Yani `fka` komutunun 3 farklı modülü var ve bunlar `blog`, `site` ve `ftp`. Autocompletion filtrelemesi olması için bir de `ssh` ekleyelim.

    $ fka <tab>
    blog ftp site ssh

Yani kurguladığımız şekilde yaparsak

    $ fka s<tab>
    site ssh

şeklinde **s** ile başlayan `ssh` ve `site` değerlerini bizimle paylaşarak bize yardımcı olacak.

## zsh'da Otomatik Tamamlama

ZSH güzel autocompletion'ları ile meşhur bir shell'dir. Bu yüzden olsa gerek, autocompletion yazmak zsh'da çok daha kolay ve güzeldir.

Öncelikle `_fka_completion` adında bir fonksiyon yaratıyoruz, bu isim her şey olabilir çünkü daha sonra bu komutu `compdef` ile `fka` komutuna tanıtacağız.

    :::bash
    _fka_completion() {

    }

Daha sonra bu fonksiyonumuz içerisinde argümanların neler olacağını belirtiyoruz.

    :::bash
    _fka_completion() {
      _arguments \
        '1: :->_modul' \
        '2: :->_komut' \
      && return 0
    }

Eğer 3 parçadan oluşan bir komut yazsaydık,

    :::bash
    _fka_completion() {
      _arguments \
        '1: :->_modul' \
        '2: :->_komut' \
        '3: :->_opsiyon' \
      && return 0
    }

gibi bir bölüm daha eklememiz gerekecekti. Şimdilik ilk hedefimiz sadece ilk bölüm olduğu için 1 adet bırakalım.

Sonrasında ise completion'un bize gönderdiği `$state` değişkeni ile hangi state'de olduğumuzu görüp ona uygun cevabı vermemiz gerekiyor.

    :::bash
    _fka_completion() {
      _arguments \
        '1: :->_modul' \
      && return 0

      case $state in
        _modul)
          _arguments '1:_modul:(blog ftp site ssh)'
        ;;
      esac
    }

dediğimiz zaman autocompletion methodumuz hazır olmuş oluyor. Şimdi bu fonksiyonu `compdef` ile tanımlayalım.

    :::bash
    compdef _fka_completion fka

Bu iki komutu yazdıktan sonra tekrar deneyelim:

    % fka <tab>
    blog ftp site ssh

Filtreleyelim:

    % fka s<tab>
    site ssh


## bash'da Otomatik Tamamlama

`zsh` da nasıl yapıldığını temel olarak gördüğümüze göre artık bunu bash üzerinde nasıl yapacağımızı görelim. Bash, Zsh'dan daha kolay ama daha kötü bir yazım şekline sahip. Filtrelemeyi de zsh'daki gibi kendisi yapmıyor, bizim yönetmemiz gerekiyor. Detaylarına bakalım.

    :::bash
    _fka_completion() {
      COMPREPLY=(blog ftp site ssh)
    }

dedikten sonra `complete` komutuyla bu fonksiyonu tanıtalım:

    :::bash
    complete -F _fka_completion fka

Bu iki komutu yazdıktan sonra tekrar deneyelim:

    $ fka <tab>
    blog ftp site ssh

Filtreleyelim:

    $ fka s<tab>
    blog ftp site ssh

Filtreleyemediğimizi görüyoruz, çünkü bash `COMPREPLY` ile gelen verilere izin vermiyor.

### bash Otomatik Tamamlamada Filtreleme Yapmak

Filtrelemeyi madem `COMPREPLY` ile yapamıyoruz, o halde biraz daha akıllı bir hale getirelim. Bu sefer `compgen` komutunu ve filtreleme yapmak için ise `grep` komutunu kullanalım.

    :::bash
    _fka_completion() {
      COMPREPLY=( $(compgen -W "blog ftp site ssh" | grep "${COMP_WORDS[COMP_CWORD]}") )
    }

`compgen` kullanırken `grep` yerine `--` kullanmak daha doğru bir kullanım olacaktır.

    :::bash
    _fka_completion() {
      COMPREPLY=( $(compgen -W "blog ftp site ssh" -- ${COMP_WORDS[COMP_CWORD]}) )
    }

Bu şekilde dönen tüm sonuçlar arasından uygun olan kelimeyi buluyoruz.

    $ fka s<tab>
    site ssh

## zsh ve bash Otomatik Tamamlama Betiklerini Birleştirmek

Elimizde artık iki ayrı fonksiyon ve iki ayrı fonksiyon tanıtma komutu var. Fakat bunları tek bir yerde birleştirmemiz en doğrusu olacaktır.

Bunu yapmamızın en kolay yolu `compdef` ve `complete` komutlarının varlığına göre `duck typing` yapmak. Eğer `compdef` komutu varsa zsh, `complete` komutu varsa bash içerisindeyizdir.

    :::bash
    if type compdef &>/dev/null; then

      # zsh fonksiyonu
      _fka_completion() {

      }
      compdef _fka_completion fka

    elif type complete &>/dev/null; then

      # bash fonksiyonu
      _fka_completion() {

      }
      complete -F _fka_completion fka

    fi

Yani `fka` komutumuzun yeni hali şu şekilde olacak:

    :::bash
    if type compdef &>/dev/null; then

      # zsh fonksiyonu
      _fka_completion() {

        _arguments \
          '1: :->_modul' \          
        && return 0

        case $state in
          _modul)
            _arguments '1:_modul:(blog ftp ite ssh)'
          ;;
        esac
      }
      compdef _fka_completion fka

    elif type complete &>/dev/null; then

      _fka_completion() {
        COMPREPLY=( $(compgen -W "blog ftp site ssh" | grep "${COMP_WORDS[COMP_CWORD]}") )
      }
      complete -F _fka_completion fka

    fi

## Alt Otomatik Tamamlamalar

Artık hem zshmodüldende bash'ta çalışan bir autocompletion'a sahibiz, fakat tam anlamıyla bitmiş sayılmaz. Çünkü başta bahsettiğimiz gibi iki parçadan oluşması gerekiyordu.

    % fka blog new-post

diyebilmem için `blog` yazdıktan sonraki ikinci kısmın da değişebilmesi gerekiyor.

### zsh'da Alt Otomatik Tamamlamaları

Dolayısıyla yeni bir parça eklemek için `zsh` da yapmamız gerekenler şu şekilde olacak:

    :::bash
    _fka_completion() {

      _arguments \
        '1: :->_modul' \
        '2: :->_komut' \
      && return 0

      case $state in
        _modul)
          _arguments '1:_modul:(blog ftp site ssh)'
        ;;

        _komut)
          case $words[2] in
            blog)
              _arguments '2:_komut:(new-post new-category)'
            ;;
          esac
        ;;
      esac
    }
    compdef _fka_completion fka

Şimdi deneyelim:

    % fka <tab>
    blog ftp site ssh

    % fka b<tab>
    % fka blog <tab>
    % fka blog new-<tab>
    new-category new-post

### bash'da Alt Otomatik Tamamlamaları

Şimdi aynı işlemi bash üzerinde yapalım.

    :::bash
    _fka_completion() {
      case $COMP_CWORD in
        1)
          COMPREPLY=( $(compgen -W "blog ftp site ssh" -- ${COMP_WORDS[COMP_CWORD]}) )
        ;;
        2)
          case ${COMP_WORDS[COMP_CWORD-1]} in
            blog)
              COMPREPLY=( $(compgen -W "new-post new-category" -- ${COMP_WORDS[COMP_CWORD]}) )
            ;;
          esac
        ;;
      esac
    }
    complete -F _fka_completion fka

Burada `COMP_CWORD` değeri bize kaçıncı kelimede olduğumuzu gösteriyor. Aynı zamanda `COMP_WORDS` ile tüm kelimeleri alabiliyoruz ve `COMP_LINE` ile tüm komutu almamız da mümkün.

Şimdi buna bir alt modül daha ekleyelim:

    :::bash
    _fka_completion() {
      case $COMP_CWORD in
        1)
          COMPREPLY=( $(compgen -W "blog ftp site ssh" -- ${COMP_WORDS[COMP_CWORD]}) )
        ;;
        2)
          case ${COMP_WORDS[COMP_CWORD-1]} in
            blog)
              COMPREPLY=( $(compgen -W "new-post new-category" -- ${COMP_WORDS[COMP_CWORD]}) )
            ;;
          esac
        ;;
        # 3. ALT MODUL
        3)
          case ${COMP_WORDS[COMP_CWORD-1]} in
            new-post)
              COMPREPLY=( $(compgen -W "title abstract" -- ${COMP_WORDS[COMP_CWORD]}) )
            ;;
          esac
        ;;
      esac
    }
    complete -F _fka_completion fka

Artık `new-post` için yeni alt modüllerimiz mevcut.

    $ fka blog new-post<tab>
    title abstract

## Dinamik Otomatik Tamamlama

Şimdiye dek yazdığımız tüm kod statik veri üzerinden ilerliyor gördüğümüz üzere. Bu kodu dinamik hale çevirmemiz gerekiyor. Bunun için gerekli bilgiler de elimizde mevcut aslında.

Bu durumda `zsh` için `compadd` komutu kullanılır.

    :::bash
    _fka_completion() {  
      compadd -- `ls /etc/`
    }

Tabii eğer burada kullanılan `ls` komutu yerine kendi komutunuzu yazdıysanız, bu durumda hangi durumda olduğunuzu gösteren veriyi göndermeniz gerekir.

    :::bash
    _fka_completion() {  
      compadd -- `fka completion "${CURRENT-1}"`
    }

Aynı durum `bash` için şu şekilde olacaktır:

    :::bash
    _fka_completion() {  
      COMPREPLY=( $(compgen -W "`ls /etc/`" -- ${COMP_WORDS[COMP_CWORD]}) )
    }

Aynı şekilde bunu kendi komutumuzla değiştirecek olursak:

    :::bash
    _fka_completion() {  
      COMPREPLY=( $(compgen -W '`fka completion "${COMP_CWORD-1}"`' -- ${COMP_WORDS[COMP_CWORD]}) )
    }

---

Eksik olan kısımlar daha sonra tamamlanacaktır. Teşekkürler!
