# Harjoitus 6: Fuzzy


## x)

### Karvinen (2023): Find Hidden Web Directories - Fuzz URLs with ffuf

* Verkkosovellusten piilotetut resurssit: Web-palvelimilla on usein hakemistoja tai tiedostoja, joihin ei ole suoria linkkejä käyttöliittymästä (esim. `/admin`, `/git/`, varmuuskopiot).
* Automaatio ffuf-työkalulla: Manuaalinen kokeilu on hidasta, mutta `fuff` (Fuzz Faster U Fool) automatisoi pyyntöjen lähettämisen sanalistoja (esim. SecLists) hyödyntäen.
* Sanalistan sijainti: `FUZZ`avainsanaa käytetään korvaamaan sanalistan jokainen rivi tietyssä osassa HTTP-pyyntöä (URL, headerit, POST-data).
* Väärien positiivisten suodatus: Moni palvelin palauttaa virheellisesti HTTP 200 OK -tilakoodin kaikille pyynnöille tai mukautettuja 404-sivuja. Tästä syystä vastauksia on suodatettava koon (`-fs`), rivimäärän (`-fl`) tai sanamäärän (`-fw`) perusteella.


Lähde: 

[Find Hidden Web Directories](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)



### Hoikkala (2023): ffuf README.md / Hoikkala (2020): Still Fuzzing Faster (U fool)

* Erittäin nopea ja suorituskykyinen: Kirjoitettu Go-kielellä, mikä mahdollistaa tehokkaan rinnakkaisuuden (asynchrony / concurrency) ja pienen muistinkulutuksen.
* Monipuolisuus: `fuff` ei ole pelkkä hakemistojen etsijä, vaan yleiskäyttöinen HTTP-fuzzer. Sitä voidaan käyttää parametri-injektioiden, HTTP-otsakkeiden (headers), virtuaali-isäntien (vhosts) ja JSON-payloadien kartoitukseen.
* Joustavat suodattimet ja täsmäytys: Vasteita voidaan täsmätä (`-mc`, `-ml`, `-ms`) tai rajata pois (`-fc`,`-fl`, `-fs`) erittäin tarkasti.


Lähde:

[Hoikkala 2023 ffuf - Fuzz Faster U Fool](https://github.com/ffuf/ffuf/blob/master/README.md)


> Voivatko suuret fuzzing-nopeudet (esim. satoja/tuhansia pyyntöjä sekunnissa) aiheuttaa Denial of Service (DoS) tilanteen?


### Käsitteet
Avaan keskeisiä käsitteitä:

<img width="547" height="425" alt="image" src="https://github.com/user-attachments/assets/604a1f3a-588d-4d57-9b7f-a3cefa4f6d3b" />

* Fuzzing (Fuzz-testaus): Automaattinen testaustekniikka, jossa sovellukselle syötetään suurta määrää syötteitä (syötevirtaa tai sanalistoja) ja tarkkaillaan järjestelmän reaktioita ja vasteita. Web-fuzzauksessa pyritään löytämään piilotettuja URL-osoitteita, parametreja tai haavoittuvuuksia.
* FUZZ-avainsana: `fuff`-työkalussa käytettävä placeholder, jonka kohdalle työkalu sijoittaa sanalistasta (wordlist) kulloinkin testattavan merkkijonon.
* Virtual Host (vhost) & Sni / Host Header: Samassa fyysisessä palvelimessa tai IP-osoitteessa voi pyöriä useita eri verkkosivustoja. Palvelin reitittää pyynnön oikealle sivustolle HTTP-pyynnön `Host:`-otsakkeen perusteella. Vhost-fuzzauksella etsitään sisäisiä tai julkaisemattomia alidomeeneja muokkaamalla tätä otsaketta.
* False Positive (Väärä positiivinen): Hakutulos, joka vaikuttaa ensisilmäyksellä löydöltä (esim. HTTP status 200 OK), mutta joka ei todellisuudessa sisällä haettua resurssia (esim. palvelimen geneerinen virhesivu).



## a) Fuzzzz
Tavoitteena on ratkaista `dirfuz-1` -haaste-ohjelmisto ja löytää piilotettu hakemisto/tiedosto.


### Suoritus ja komennot
Latasin haasteohjelma ja määritin sille suoritusoikeudet:


```bash
# Maalin lataaminen
wget https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/dirfuz-1

# Annan suoritusoikeudet tiedostolle 
chmod u+x dirfuz-1

# Käynnistän maalin
./dirfuz-1

```

<details>
<summary>Ohjelman lataus ja oikeuksien määritys:</summary>

<img width="926" height="372" alt="image" src="https://github.com/user-attachments/assets/866e807f-117d-44bc-9aa1-9e51f375f6bd" />


</details>

* Ohjelma käynnisti paikallisen HTTP-palvelimen osoitteeseen `http://127.0.0.2:8000`
* Ajan seuraavaksi alustavan `fuff`-skannin uudessa terminaalissa käyttäen Kalin valmista sanalistaa `dirb`:

```bash

ffuf -w /usr/share/wordlists/dirb/common.txt -u http://127.0.0.2:8000/FUZZ

```

<details>
<summary>Fuff-skanni:</summary>

<img width="659" height="350" alt="image" src="https://github.com/user-attachments/assets/a2553133-d252-4c89-8916-21bdb55aba3a" />


<img width="766" height="230" alt="image" src="https://github.com/user-attachments/assets/eb3d290d-8e46-491c-a11c-5fe4cd6ecd5f" />


</details>

* Ajoin skannin siten, että en suodattanu tuloksia ja palvelin vastasi kaikkiin 4614 pyyntöön HTTP-statuskoodilla `200 OK`
* Havainto: Kohdepalvelin käyttää Catch-all -reititystä, joka palauttaa oletussivun `200 OK`-statuksella `404 Not Found`-koodin sijaan. Tämä aiheuttaa "False Positive" -ilmiön eli vääriä positiivisia tuloksia.
* Suoritan seuravan skannin suodatuksella:

```bash

ffuf -w /usr/share/wordlists/dirb/common.txt -u http://127.0.0.2:8000/FUZZ -fs 132

```

<details>
<summary>Suodatettu fuff-skanni:</summary>


<img width="821" height="431" alt="image" src="https://github.com/user-attachments/assets/96ced81c-ba4f-42da-924d-0b5af9b066b2" />


</details>

* `-fs 132` suodatti pois kaikki 4614 geneeristä vastausta, jolloin terminaaliin jäi näkyviin vain poikkeavat ja todelliset resurssit.
* Löydetty piilotettu kohde `admin`: Fuzzer löysi ainoana poikkeavana tuloksena polun, jonka koko ja status poikkesivat kohinasta.
* Curl:lla `admin` hakemiston tarkistelu:

```bash
curl -s http://127.0.0.2:8000/admin

```

<details>
<summary>Admin hakemisto:</summary>

<img width="445" height="209" alt="image" src="https://github.com/user-attachments/assets/300c0bdd-6308-46be-9a23-31eeb5c5881c" />


</details>

* Tulos: Palvelin palautti salaisen sivun tekstillä: "You've found it!".


Lähde:

[Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)


## b) FuffMe-ympäristön asennus

* Tavoite: Asentaa `ffufme`-harjoitusympäristö Docker-konttina.


### Asennuskomennot

```bash
# Päivitän koneen ja asennan tarvittavat työkalut
sudo apt-get update
sudo apt-get install -y docker.io git ffuf

```

<details>
<summary>Työkalujen asennus:</summary>

<img width="978" height="526" alt="image" src="https://github.com/user-attachments/assets/5d07c683-504f-4764-9abd-cc9eace530f2" />


</details>

```bash
# Kloonaan ffufme-repositorion ja rakennan kontin
git clone https://github.com/adamtlangley/ffufme
cd ffufme/
sudo docker build -t ffufme .

```

<details>
<summary>Respon kloonaus ja kontin rakennus:</summary>

<img width="1004" height="656" alt="image" src="https://github.com/user-attachments/assets/6399e6b0-9410-48fc-b67c-edc54919ff5f" />


</details>

```bash
# Käynnistän kontin portissa 80
sudo docker run -d -p 80:80 ffufme

```

<details>
<summary>Kontin käynnistys:</summary>

<img width="534" height="61" alt="image" src="https://github.com/user-attachments/assets/fadf8df5-482f-4f1e-b650-697426bfb4ae" />


</details>

```bash
# Lataan tarvittavat sanalistat
mkdir -p wordlists
cd wordlists
wget http://ffuf.me/wordlist/common.txt
wget http://ffuf.me/wordlist/parameters.txt
wget http://ffuf.me/wordlist/subdomains.txt

```

<details>
<summary>Sanalistojen lataaminen:</summary>


<img width="733" height="661" alt="image" src="https://github.com/user-attachments/assets/6312b175-405f-4ccc-81a6-86711ec7af23" />


</details>


* Testaan maalin toimivuuden ajamalla:

```bash
curl -si http://localhost | grep -i "title"

```

<details>
<summary>Maalin toimivuuden testaaminen:</summary>

<img width="396" height="91" alt="image" src="https://github.com/user-attachments/assets/5d483a8a-0775-4f01-bded-1c5b9ce4f434" />


</details>

* `<title>FFUF.me</title>` osoittaa testi ympäristön olevan valmis.

Lähde: 

[Fuffme - Install Web Fuzzing Target on Debian](https://terokarvinen.com/2023/fuffme-web-fuzzing-target-debian/)


## c) Basic Content Discovery

* Tavoite: Etsiä piilotetut hakemistot ja tiedostot

Komento:

```bash

ffuf -w $HOME/wordlists/common.txt -u http://localhost/cd/basic/FUZZ

```

<details>
<summary>fuff tulos:</summary>

<img width="744" height="444" alt="image" src="https://github.com/user-attachments/assets/df7a72bb-79e8-411c-ba23-2487d5efb9dd" />


</details>

* Löydökset: Hakemisto `/class` sekä lokitiedosto `/development.log`.

Lähde: 

[Content Discovery - Basic](http://ffuf.me/cd/basic)


## d) Content Discovery With Recursion

* Tavoite: Etsiä hakemistoja rekursiivisesti eli käydä automaattisesti läpi myös löytyneiden alihakemistojen sisältö.

Komento:

```bash
ffuf -w $HOME/wordlists/common.txt -u http://localhost/cd/recursion/FUZZ -recursion


```

<details>
<summary>fuff-tulos:</summary>

<img width="742" height="572" alt="image" src="https://github.com/user-attachments/assets/fa13a7c2-ba6e-4036-828f-9c6571464783" />


</details>

* Rekursion toiminta: `-recursion`-parametri käskee `fuff`:ia a lisäämään jokaisen löytyneen hakemiston (status 200/301/302) jonoon ja suorittamaan uuden fuzzauksen kyseiseen polkuun
* Löydös: `/admin`-hakemisto, `/admin/users`-hakemisto ja `/admin/users/96`

Lähde:

[Content Discovery With Recursion](http://ffuf.me/cd/recursion)

## e) Content Discovery With File Extensions

* Tavoite: Etsiä tiedostoja tiettyjen tiedostotarkenteiden perusteella (esim. .php, .txt, .log).

Komento:

```bash
ffuf -w $HOME/wordlists/common.txt -u http://localhost/cd/ext/FUZZ -e .php,.txt,.log


```

<details>
<summary>fuff-tulos:</summary>

<img width="741" height="433" alt="image" src="https://github.com/user-attachments/assets/c20079ef-5e5c-4ccd-8fe4-7747717d72e4" />


</details>

* Skanni löysi tiedoston `/logs/users.log`
* `-e`-parametri i liittää jokaisen sanalistan sanan perään määritellyt päätteet. Esimerkiksi sana `config` testataan muodossa: `config`, `config.php`, `config.txt` ja `config.log`.

Lähde:

[Content Discovery With File Extensions](http://ffuf.me/cd/ext)

## f) No 404 Status

* Tavoite: Löytää piilotettu sisältö sovelluksesta, joka palauttaa `HTTP 200 OK `myös silloin, kun sivua ei ole olemassa.

Komento:

```bash
ffuf -w ~/wordlists/common.txt -u http://ffuf.me/cd/no404/FUZZ

```

<details>
<summary>fuff-tulos:</summary>

<img width="741" height="187" alt="image" src="https://github.com/user-attachments/assets/39ae0508-d7c1-4ddc-9aca-8cbb7e7dfb76" />


</details>

* Fuffin skanni palautti jokaiselle sanalle tilakoodin 200 OK. Vasteen rivimäärä oli kuitenkin virheellisillä sivuilla vakio.
* Seuraavaksi ajan saman komennon, mutta suodatan tulokset siten että lisän `-fs`-parametriin loppuun`669`-option joka suodattaa pois kaikki tulokset jotka ovat `669` tavua pitkiä.

Komento:

```bash
ffuf -w ~/wordlists/common.txt -u http://ffuf.me/cd/no404/FUZZ -fs 669

```

<details>
<summary>fuff-skanni:</summary>

<img width="789" height="427" alt="image" src="https://github.com/user-attachments/assets/00b88c95-6d4b-475e-a45c-62352a510373" />

</details>

* Suodatuksen avulla sain piilotettua kaikki virheelliset tulokset ja sain piilotetun sivun: `secret` esiin.
  
Lähde:

[No 404 Status](http://ffuf.me/cd/no404)

## g) Param Mining
Tavoite: Etsiä toiminnallinen GET-parametri, joka muuttaa sivun käyttäytymistä. EI OO VALMIS

Komento:

```bash
ffuf -w ~/wordlists/parameters.txt -u http://ffuf.me/cd/param/data?FUZZ=1

```

<details>
<summary>fuff-testitulos:</summary>

<img width="765" height="419" alt="image" src="https://github.com/user-attachments/assets/b4ea05ae-d731-41f8-a23c-d2efc687de2c" />


</details>

* Skanni löytää puuttuvan parametrin: `debug`
  

Lähde:

[Param Mining](http://ffuf.me/cd/param)

## h) Rate Limited

Tavoite: Fuzzata kohdetta, joka rajoittaa pyyntöjen määrää (Rate Limiting) ja palauttaa virhekoodin `429 Too Many Requests` liian nopeista pyynnöistä.

Komento:

```bash
ffuf -w ~/wordlists/common.txt -u http://ffuf.test/cd/rate/FUZZ -mc 200,429

```

<details>
<summary>fuff-tulos:</summary>

<img width="748" height="395" alt="image" src="https://github.com/user-attachments/assets/49968fe4-aa8d-474d-abaa-496c899d72c1" />


</details>

* Komento lähettää jatkuvasti pyyntöjä kohteeseen kunnes palvelimeen on tullu liian monta pyyntöä lyhessä aikavälissä, jonka jälkeen palvelin väliaikaisesti estää pyyntöjen lähettämisen.


Komento:

```bash
ffuf -w ~/wordlists/common.txt -t 5 -p 0.1 -u http://ffuf.test/cd/rate/FUZZ -mc 200,429

```
* `-p 0.1`: Asettaa pyyntöjen väliin 0,1 sekunnin viiveen
* `-t 1`: Rajoittaa suoritussäikeiden (threads) määrän yhteen, jotta pyynnöt lähtevät sekvenssissä ilman rinnakkaisuutta.
* `-mc 200`: Huomioi vain onnistuneet HTTP 200 -vastaukset ja jättää 429-virheet huomiotta.
  

<details>
<summary>fuff-tulos:</summary>

<img width="810" height="413" alt="image" src="https://github.com/user-attachments/assets/4e971581-0a0d-4be3-8fdd-e1858434de47" />


</details>

* Skanni ei palauttanut odotettua tulosta: `oracle`-tiedostoa en ole ihan varma miksi ei palauttanut.

Lähde:

[Rate Limited](http://ffuf.me/cd/rate)


## i) Subdomains - Virtual Host Enumeration

* Tavoite: Löytää järjestelmään määritellyt virtuaaliset isännät (Virtual Hosts / Vhosts) fuzzaamalla HTTP-pyynnön `Host:`-otsaketta.

Komento:

```bash
fuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://ffuf.me

```

<details>
<summary>fuff-tulos:</summary>




</details>

* Jokainen tulos on kooltaan 1495 tavua.
* Kokeilen seuraavaksi suodattaa tulokset siten, että ainoastaan näen tulokset jotka ei ole 1495 tavua kooltaan.

Komento:

```bash
ffuf -w ~/wordlists/subdomains.txt -H "Host: FUZZ.ffuf.me" -u http://ffuf.me -fs 1495

```

<details>
<summary>fuff-tulos:</summary>




</details>

* Komento löysi domainin: `redhat`

Lähde:

[Subdomains - Virtual Host Enumeration](http://ffuf.me/sub/vhost)
