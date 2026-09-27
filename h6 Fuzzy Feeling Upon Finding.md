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


## a) Toimintavaltuudet, Scope ja Riskienhallinta

Ennen minkäänlaisen skannaus- tai testaustyökalun ajamista on määriteltävä testauksen oikeudellinen perusta ja tekniset rajoitteet. Ilman kirjallista lupaa tehty automaattinen fuzzaus voi täyttää tietoliikenteen häirinnän tai luvattoman tietojärjestelmään tunkeutumisen tunnusmerkistön.

### Scope ja Rules of Engagement (RoE)

> [!IMPORTANT]
> Scope (Testauksen kohde ja rajautuvuus):
> Testauksen piiriin kuuluu **ainoastaan** kohde-URL: `https://ffuf.io.fi/play` sekä sen alaisuudessa toimivat suorat ali-URL:t ja sovelluskomponentit. Kaikki muu verkko-osoitteisto tai palveluntarjoajan infrastruktuuri (esim. isäntäpalvelimen muut IP-osoitteet tai naapuripalvelimet) ovat rajoitusalueen ulkopuolella (Out-of-Scope).


> [!NOTE]
> **Rules of Engagement (RoE / Toimintasäännöt):**
> > 1. Hyökkäysmenetelmät: Automaattinen HTTP-pyyntöjen fuzzgaus on sallittu ainoastaan sovellustasolla määritettyyn osoitteeseen.
> > 2. Pyyntötiheys (Rate Limiting): Saapuvaa liikennettä on rajoitettava tarvittaessa (`-rate` lipulla), jotta kohdepalvelimen suorituskyky ei vaarannu.
> > 3. Service Denial (DoS): Testauksessa ei saa pyrkiä palvelunestohyökkäykseen eikä käyttää palvelimen kaatavia syötteitä.
> > 4. Tietojen säilytys: Löydetyt CSRF-tokenit, sessioavaimet ja mahdolliset piilotetut tiedostot säilytetään luottamuksellisesti vain raportointia varten.


### Oikeus tietoturvatestaukseen

Testaoikeus perustuu kohteen ylläpitäjän (Joohoi) antamaan **julkiseen ja kirjalliseen lupaan/haasteeseen** harjoitussivustolla `https://ffuf.io.fi/play`. Tämä toimi eettisen hakkeroinnin periaatteiden mukaisena valtuutuksena (*Authorization Header / Written Consent*), mikä erottaa luvallisen tietoturvatestauksen luvattomasta tunkeutumisesta.


### Riskianalyysi ja mitigointi

Koska testaus suoritetaan julkisen Internet-verkon yli, siihen liittyy teknisiä ja operatiivisia riskejä:


| Tunnistettu riski | Kuvaus ja vaikutus | Mitigointitoimenpide (Ennaltaehkäisy) |
| :--- | :--- | :--- |
| **Palvelimen ylikuormitus (DoS)** | Korkealla säikeistöllä (threads) tehty fuzzaus kuluttaa palvelimen CPU/RAM-resursseja ja kaataa sovelluksen. | Rajoitetaan rinnakkaisten pyyntöjen määrää (`-t 10..20`) ja asetetaan maksimipyyntömäärä sekunnissa (`-rate 50`). |
| **IP-osoitteen suodatus (WAF/IDS)** | Target-palvelimen tai reitillä olevan palomuurin IDS-järjestelmä tulkitsee liikenteen hyökkäykseksi ja estää IP-osoitteen. | Käytetään tarkasti rajattuja sanastoja (*wordlists*) hallitsemattoman liikennemäärän sijaan ja kunnioitetaan viiveitä. |
| **Scope Creep (Luvaton kohde)** | Fuzzauksessa käytettävä sanasto tai rekursio ohjaa pyynnöt ulkopuoliseen järjestelmään tai kolmannen osapuolen API:in. | Asetetaan tarkat rekursiosyvyydet (`-recursion-depth 2`) ja varmistetaan pyyntöjen rajautuminen vain määriteltyyn isäntänimeen. |


## b) `ffuf`-työkalun asennus ja Preflight-tuki

Tehtävää varten tarvittiin `ffuf`-versiosta tuore kooste, joka tukee `-preflight`-ominaisuutta (vaatii v2.3.0 tai uudemman). Varmistettiin asennus kääntämällä uusin versio lähdekoodista tai lataamalla uusin julkaisu-binääri GitHubista.

### Asennusohje ja version tarkistus

```bash
# Haetaan uusin pre-compiled binääri (esim. v2.1.0 tai v2.3.0+ riippuen uusimmasta julkaisusta)
wget https://github.com/ffuf/ffuf/releases/download/v2.1.0/ffuf_2.1.0_linux_amd64.tar.gz

# Tarkistetaan asennettu versio ja preflight-lipun löytyminen
ffuf -h | grep -c preflight

```

<details>
<summary>Asennus ja version tarkistus:</summary>

<img width="1424" height="450" alt="image" src="https://github.com/user-attachments/assets/42224f89-d63e-4cb9-a40c-1735bd16dd06" />


<img width="256" height="60" alt="image" src="https://github.com/user-attachments/assets/49e2b200-09a9-4b1f-891b-fda7a8d4d3ce" />


</details>

> Havainto: Komento `ffuf -h | grep -c preflight` palautti arvon `> 0` (esim. `4` tai enemmän), mikä vahvistaa, että asennettu versio tukee `-preflight`- ja `-preflight-var`-argumentteja.


## c1) Content Discovery (Sisällön haravointi)

Tavoite: Löytää sovelluksen piilotetut hakemistot ja tiedostot.

Menetelmä: Ajetaan sanastohyökkäys URL-osoitteen perään sijoitettavalla `FUZZ`-avainsanalla.

```bash
ffuf -u https://ffuf.io.fi/play/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,301,302

```

<details>
<summary>Testitulos:</summary>




</details>

* Suodatin pois turhat 404-statuskoodit (`-mc`), jolloin jäljelle jäivät vain olemassa olevat resurssit.


## c2) The Interesting Non-200

Tavoite: Löytää resursseja, jotka eivät palauta vakiomuotoista `200 OK` -koodia, mutta osoittavat resurssin olevan olemassa (esim. `403 Forbidden`, `401 Unauthorized`, `301 Moved Permanently` tai `500 Internal Server Error`).

```bash
ffuf -u https://ffuf.io.fi/play/FUZZ -w /usr/share/wordlists/dirb/common.txt -fc 404

```

<details>
<summary>Testitulos:</summary>




</details>


## c3) 
