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
