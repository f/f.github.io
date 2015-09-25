---
layout: post
title: Vagrant'ta PostgreSQL `createdb` Locale Problemi Çözümü
abstract: Vagrant'ta Ubuntu 12.04 üzerinde Postgres'in createdb'sini çalıştırırken daha önce karşılaşmadığım bir sorunla karşılaştım. Bu sorunu çözen bir "unprofessional" bir fix.
---

Bir DBA olmadığım için DB işlemleri çalışırken beni çok yorabiliyor, bu yüzden bu tarz ufak şeyleri bir yerde saklamam gerekiyor. Bu post aslında "kendime not" tadında.

Vagrant'ta Ubuntu 12.04 üzerinde Postgres'in createdb'sini çalıştırırken daha önce karşılaşmadığım
bir sorunla karşılaştım. Araştırmalarım sonucunda bunun bir locale sorunu olduğunu gördüm.

`syncdb` yaptığımda initial data yüklenirken karakter sorunu yaşıyordum. Bunun için önce şu komutu denedim:

    :::bash
    sudo -u postgres createdb -E UTF8

Bunun karşılığında aldığım cevap ise şu:

    :::bash
    createdb: database creation failed: ERROR:  encoding UTF8 does not match locale en_US
    DETAIL:  The chosen LC_CTYPE setting requires encoding LATIN1.

Dedim o halde burada bir başka şey olmalı:

    :::bash
    sudo -u postgres createdb --locale=en_US.utf8

Bu kez aldığım cevap:

    :::bash
    createdb: database creation failed: ERROR:  encoding LATIN1 does not match locale en_US.utf8
    DETAIL:  The chosen LC_CTYPE setting requires encoding UTF8.

O halde başka bir çözümü olmalı diye düşündüm ve internette biraz araştırdıktan sonra; şu linke ulaştım:

[http://linux.m2osw.com/postgresql_and_unicode]()

Burada şu komutu gördüm:

    :::sql
    CREATE DATABASE mydb WITH ENCODING = 'UTF-8'
    LC_CTYPE = 'en_US.utf8' LC_COLLATE = 'en_US.utf8'
    TEMPLATE template0;

Dolayısıyla Postgres Shell'e girip bu komutu çalıştırarak UTF-8 bir DB oluşturabiliyorum, fakat bu çok uzun bir iş:

    :::bash
    sudo -u postgres psql
    postgres=# CREATE DATABASE mydb WITH ENCODING = 'UTF-8'
    postgres=# LC_CTYPE = 'en_US.utf8' LC_COLLATE = 'en_US.utf8'
    postgres=# TEMPLATE template0;

Bunun yerine createdb-utf8 adında bir alias oluşturalım ve `.profile` dosyamıza ekleyelim:

    :::bash
    function createdb-utf8() {
      sudo -u postgres psql -c "CREATE DATABASE $1 WITH ENCODING='UTF-8' \
      LC_CTYPE='en_US.utf8' LC_COLLATE='en_US.utf8' TEMPLATE template0;"
    }

Böylece

    :::bash
    ~$ createdb-utf8 mydb

diyerek yeni bir DB oluşturabilirsiniz.

Hatam varsa affola, sevgiler. :)
