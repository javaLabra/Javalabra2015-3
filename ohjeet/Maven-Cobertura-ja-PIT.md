﻿# Maven, Cobertura ja PIT

### Projektin luominen
Projekti luodaan normaalisti NetBeansin **New Project**-nappulasta. Nyt kuitenkin ei valita kategoriaa **Java**, vaan hieman alempaa etsitään kohta **Maven**. Oikeasta valikosta voidaan nyt valita **Java Application**.

Sivulla **Name and Location** kannattaa antaa ohjelmalle hyvä ja kuvaava nimi. Aseta **Project Location**:ksi Git-repositoriosi osoite. Myös **Group id** täytyy vaihtaa ohjelman nimen mukaiseksi tai ainakin joksikin muuksi kuin _com.mycompany.enterprising.domain_ . Se muuttaa ohjelman vakiopakkauksen nimen, tämä on tärkeää mutaatiotestauksen toiminnan kannalta.

Maven ei eroa "normaalista" Java-projektista kovinkaan paljoa. Suurin ero on pom.xml-tiedosto joka kuvaa xml:nä projektin riippuvuudet eri kirjastoista. Lisäämme sinne valmiiksi muutaman testausta auttavan kirjaston, mutta jos aiot käyttää itse jotain apukirjastoja, ne voidaan lisätä tänne myös.

Nyt projektisi pom.xml:n pitäisi näyttää jotakuinkin seuraavalta:
``` xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>ohjelmannimi</groupId>
    <artifactId>SuperOhjelma</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>SuperOhjelma</name>
    <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

Tämän xml:n alkuosa on jokaisella projektille yksilöllinen. Siihen emme koske. Sen sijaan haluamme lisätä muuutaman riippuvuuden ja pluginin projektiin. Samalla päivitämme uudempaan junit-versioon. Korvataan siis osa:
``` xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>3.8.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
Tällä:
``` xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.10</version>
        <scope>test</scope>
    </dependency>
</dependencies>
    
<build>
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.7</source>
                <target>1.7</target>
            </configuration>
            <version>2.5</version>
        </plugin>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>cobertura-maven-plugin</artifactId>
            <version>2.6</version>
        </plugin>
        <plugin>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-maven</artifactId>
            <version>0.30</version>
        </plugin>
    </plugins>
</build>
```

Tämän jälkeen projektille voi tehdä testirivikattavuus- ja mutaatiotestausraportteja. Tehdään vielä näiden generoimisesta hieman helpompaa.

Lisää projektiin uusi tiedosto **New File...**-näppäimellä. Valitse **XML -> XML Document** ja anna sille nimeksi **nbactions**. Siirry seuraavan ikkunaan ja valitse **Well-formed Document**. Paina Finish. Nyt projektissa pitäisi olla uusi _nbactions.xml_-tiedosto. Jos se ei ole Project Files-kansiossa, siirrä se sinne. 

Avaa tiedosto ja korvaa sen sisältö tällä: 
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<actions>
    <action>
        <actionName>CUSTOM-cobertura</actionName>
        <displayName>cobertura</displayName>
        <goals>
            <goal>cobertura:cobertura</goal>
        </goals>
    </action>
    <action>
        <actionName>CUSTOM-pit</actionName>
        <displayName>pit</displayName>
        <goals>
            <goal>org.pitest:pitest-maven:mutationCoverage</goal>
        </goals>
    </action>
</actions>
```

Tallenna ja aja projektille **Clean and Build**. Nyt kun klikkaat projektia oikealla hiirennäppäimellä, **Custom**-valikon alta pitäisi löytyä _cobertura-_ ja _pit-_vaihtoehdot.

### Raportit

Lainaus Pit-testien sijainnista:
> Mutaatiotestaa ohjelmasi suorittamalla ensin testit ja sen jälkeen klikkaamalla NB:stä projektin kohdalla oikeaa nappia ja valitsemalla custom/pit

> tai vaihtoehtoisesti ollessasi projektihakemistossa antamalla komentoriviltä komennot mvn test ja mvn org.pitest:pitest-maven:mutationCoverage

> Edellinen onnistuu myös yhdellä komennolla mvn test org.pitest:pitest-maven:mutationCoverage

> Tulokset tulevat projektihakemistosi alihakemistoon target/pit-reports/aikaleima/index.html. Aikaleima on numerosarja joka kertoo suoritushetken, tyyliin 201311181742 (testi ajettu 18.11.2013 klo 1742). Avaa tulokset web-selaimella. Firefoxilla tämä tapahtuu komennolla open file. Voit myös avata selaimen terminaalissa menemällä ensin projektihakemistoon ja antamalla komento chromium-browser target/pit-reports/aikaleima/index.html. Kun ajat mutaatiotestit uudelleen, muista avat oikea raportti!

> Voit poistaa vanhat raportit painamalla vasara+harja-symbolia (clean and build) tai komentoriviltä komennolla mvn clean

> Raportista näet mitkä mutantit jäävät henkiin.

Kun lisäät pit-raportin viikkopalautukseen, siirrä koko aikaleimakansio target/pit-reports/-hakemistosta _dokumentaatio/pit/_-kansioon.

Lainaus [OTM:n viikolta 4](https://github.com/mluukkai/OTM2013/wiki/viikon-4-laskareissa-teht%C3%A4v%C3%A4-teht%C3%A4v%C3%A4#3-testien-testaaminen) Coberturan tuloksista:
> Projektitiedostoon on valmiiksi konfiguroitu käytettäväksi Cobertura joka mittaa sekä lause- että haarautumakattavuuden.

> Kattavuuden mittaus suoritetaan klikkaamalla NB:stä projektin kohdalla oikeaa nappia ja valitsemalla custom/cobertura

> tai vaihtoehtoisesti ollessasi projektihakemistossa antamalla komentoriviltä komento mvn cobertura:cobertura

> Tulokset tulevat projektihakemistosi alihakemistoon target/site/cobertura/index.html. Avaa tulokset web-selaimella. Firefoxilla tämä tapahtuu komennolla open file. Voit myös avata selaimen terminaalissa menemällä ensin projektihakemistoon ja antamalla komento chromium-browser target/site/cobertura/index.html

Kun lisäät cobertura-raportin dokumentaatioon, laita sinne koko _target/site/cobertura_-kansion sisältö. Tee sille oma kansio, esimerkiksi _dokumentaatio/cobertura/_

## Pitin konfigurointi
Pit-testaukseen voi suhteellisen helposti määrittää, mitkä luokat testataan. Jos tavoittelee 100% mutanttien tappamista, tai muuten vaan nätimpää dokumentaatiota, voi näin jättää muun muassa käyttöliittymän ja main-luokan pois mutaatiotestauksesta.

Lisäämällä pom.xml:ään plugin-tagien sisään configurationin, voi määrittää tarkasti, minkä pakettien sisällöt testataan. Eli jos testiraporttiin halutaan kuvitteellisesta ristinollaohjelmasta testata vain logiikka ja tekoäly, kerrotaan tämä targetClasses:n ja targetTests:n kautta näin:

Korvataan pom.xml:n pit-plugin kohta:
``` xml
            <plugin>
                <groupId>org.pitest</groupId>
                <artifactId>pitest-maven</artifactId>
                <version>0.30</version>
            </plugin>
```

Seuraavan tyylisellä (muuta param-kohdat omaa ohjelmaasi vastaaviksi):

``` xml
<plugin>
    <groupId>org.pitest</groupId>
    <artifactId>pitest-maven</artifactId>
    <version>0.30</version>
    <configuration>
        <targetClasses>
            <param>ristinolla.logiikka.*</param>
            <param>ristinolla.tekoaly.*</param>
        </targetClasses>
        <targetTests>
            <param>ristinolla.logiikka.*</param>
            <param>ristinolla.tekoaly.*</param>
        </targetTests>
    </configuration>
</plugin>
```
Eli jos haluat testata paketin joka NetBeanssissa on laivanupotus.logiikka, täytyy pom.xml:ään laittaa aivan suoraan `<param>laivanupotus.logiikka*</param>`.

####Pitin päivittäminen

Joillakin on ollut ongelmia tiedostonkäsittelijöiden/lukioiden testaamisessa. Mikäli pit-raportti näyttää luokalle 0%:n kattavuutta vaikka ei pitäisi, kannattaa päivittää pit-testit uusimpaan versioon.

Pit-testit päivitetään vaihtamalla versionumero pom.xml:stä 1.1.4 ja ajamalla "clean and build".

``` xml
        <plugin>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-maven</artifactId>
            <version>1.1.4</version>
        </plugin>
```

Mikäli pit-ongelmat eivät ratkea päivityksellä, ota yhteyttä ohjaajiin.

### Coberturan konfigurointi
Samoin kuin Pitissä, voidaan myös coberturassa määrittää, mitkä luokat testataan. Tätäkin varten tarvitaan pom.xml:ään configuration-blokki. Coberturassa xml-syntaksi vaatii vielä instrumentationin, includesin ja tarkemmin tiedot mukaan otettavista luokista.

Korvaa
``` xml
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>cobertura-maven-plugin</artifactId>
                <version>2.6</version>
            </plugin>
```

Tämän tyylisellä (vaihda include-tagien sisältö omaa ohjelmaasi vastaavaksi):

``` xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>cobertura-maven-plugin</artifactId>
    <configuration>
        <instrumentation>
            <includes>
                <include>ristinolla.logiikka/*.class</include>
                <include>ristinolla.tekoaly/*.class</include>
            </includes>
        </instrumentation>
    </configuration>
    <version>2.6</version>
</plugin>
```

Jos haluat testata kaikki pakkauksen sisällä olevien pakkauksien kaikki luokat, include tulee kirjoittaa muodossa: `<include>ristinolla/**/*.class</include>`.

### Virhetilanteet

* Jos pittiä ajaessa tulee kasa punaista tekstiä, joka näyttää suunnilleen tältä:

```
Failed to execute goal org.pitest:pitest-maven:0.30:mutationCoverage (default-cli) on project OhHa:
Execution default-cli of goal org.pitest:pitest-maven:0.30:mutationCoverage failed:
No mutations found. This probably means there is an issue with either the supplied classpath or filters.
```

* Se tarkoittaa, että pom.xml:n groupID ja projektin pakettien nimet eivät täsmää keskenään. Tai että konfiguroinnin parametrit osoittavat väärään paikkaan. Tai että testit eivät ole koodia vastaavissa paketeissa.

* Jos et ole konfiguroinut pittiä, tarkista että ohjelmasi oletuspaketin nimi ei ole eri kuin pom.xml:ssä oleva groupID. Tässä isoilla kirjaimilla on väliä, eli jos paketin nimi on "ohha" ja pom.xml:ssä lukee `<groupId>OhHa</groupId>`, ei pit-raportin generointi tule onnistumaan. Jos ero löytyy, korjaa jommankumman nimi.

* Jos olet konfiguroinut pittiä, tarkista ettet ole vahingossa lisännyt liian pitkää polkua.

* Tarkista että testisi ovat ohjelmakoodia vastaavissa paketeissa. Eli jos testaat ristinolla.logiikka- paketissa olevaa Ruutu-oliota, pitää Test Packagesissa olla paketti ristinolla.logiikka, jonka sisällä on RuutuTest.

* Jos Pit-raportti generoidaan oikein, mutta se näyttää 0% kaikessa vaikka toimivia testejä on, tarkista että testitiedostojen alussa on testitiedoston pakettia vastaava pakettimäärittely, esim: `package ristinolla.logiikka` 

### JavaDoc
Jos NetBeanssin oma "Generate JavaDoc"-näppäin on harmaana, ei kannata huolestua, voimme lisätä sen helposti samaan custom-valikkoon kuin pit ja cobertura.

Lisää pom.mxl:ään tämä plugin:

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>2.9.1</version>
</plugin> 
```

Tämän jälkeen build-osan pitäisi näyttää suunnilleen tältä (paitsi jos olet konfiguroinut pittiä ja coberturaa, jolloin niiden sisällä on configuration-blokki):
``` xml
<build>
    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.7</source>
                <target>1.7</target>
            </configuration>
            <version>2.5</version>
        </plugin>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>cobertura-maven-plugin</artifactId>
            <version>2.6</version>
        </plugin>
        <plugin>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-maven</artifactId>
            <version>0.30</version>
        </plugin>
        <plugin>
             <groupId>org.apache.maven.plugins</groupId>
             <artifactId>maven-javadoc-plugin</artifactId>
             <version>2.9.1</version>
        </plugin> 
    </plugins>
</build>
```

Sitten lisätään plugin vielä NetBeanssin valikkoon nbactions.xml:n kautta. Korvaa nbactions.xml:n sisältö tällä:
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<actions>
    <action>
        <actionName>CUSTOM-cobertura</actionName>
        <displayName>cobertura</displayName>
        <goals>
            <goal>cobertura:cobertura</goal>
        </goals>
    </action>
    <action>
        <actionName>CUSTOM-pit</actionName>
        <displayName>pit</displayName>
        <goals>
            <goal>org.pitest:pitest-maven:mutationCoverage</goal>
        </goals>
    </action>
    <action>
        <actionName>CUSTOM-Javadoc</actionName>
        <displayName>Javadoc</displayName>
        <goals>
            <goal>javadoc:javadoc</goal>
        </goals>
    </action>
</actions>
```

Nyt voit generoida JavaDocin klikkaamalla projektin nimeä oikealla hiirennäppäimellä ja valitsemalla `custom-JavaDoc`.
