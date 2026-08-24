## x) Lue / katso / kuuntele ja tiivistä
### 1. Podcast: Herrasmieshakkerit – Jakso 0x45: Erikoistilanteiden asiantuntija, vieraana Juhani Mäkinen (16.6.2026)
**Tiivistelmä:**
* Jaksossa käsitellään Applen tietoturvaa, *core crypto* -kirjaston verifiointia sekä macOS-kernelin muistinkorruptiohaavoittuvuuksia.
* Keskustelussa sivutaan kriisinhallintaa ja erikoisjoukkotoimintaa Afganistanissa (*Kontakti*-kirjan teemojen kautta).
* Lisäksi käydään läpi Suomen Pelimuseon uudistusta ja Peter Steinbergerin TED-puheenvuoroa.

### 2. Hutchins et al. 2011: Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains (Abstract & Chapter 3.2)
 **Tiivistelmä:**
* Artikkelissa esitellään tunnilla läpikäytyä Lockheed Martinin kehittämä *Intrusion Kill Chain* -mallia, joka jakaa kohdistetun hyökkäyksen 7 vaiheeseen:
  1. **Reconnaissance** (Tiedustelu ja kohteen valinta)
  2. **Weaponization** (Aseistaminen / hyökkäysvektorin luonti)
  3. **Delivery** (Toimitus kohteelle, esim. phishing)
  4. **Exploitation** (Aukon hyödyntäminen koodin suorittamiseksi)
  5. **Installation** (Haittaohjelman/haavoittuvuuden asentaminen koneelle)
  6. **Command & Control (C2)** (Etähallintayhteyden muodostaminen)
  7. **Actions on Objectives** (Tavoitteen toteutus, esim. datavarkaus tai tuho)
 * Puolustajan etu on siinä, että hyökkääjän on onnistuttava jokaisessa 7 vaiheessa peräkkäin. Jos puolustaja katkaisee ketjun missä tahansa kohdassa, hyökkäys epäonnistuu.
 * **Oma huomio / idea:** Tappoketju osoittaa hyvin, että puolustuksen ei tarvitse olla aukoton ensimmäisessä vaiheessa. Tärkeintä on rakentaa syväpuolustus (defense-in-depth), jotta hyökkäys pysäytetään ennen C2- tai Actions on Objectives -vaihetta.

### 3. Santos et al.: The Art of Hacking – 4.3 Surveying Essential Tools for Active Reconnaissance
**Tiivistelmä:**
* Videokokonaisuudessa käydään läpi aktiivisen tiedustelun perustyökaluja, kuten Nmap, Masscan ja Netcat.
* Aktiivinen tiedustelu poikkeaa passiivisesta (OSINT) siinä, että se lähettää paketteja suoraan kohdejärjestelmään ja jättää aina jälkiä kohdekoneen tai palomuurin lokitietoihin.
* Porttiskannauksella selvitetään avoimet portit, niissä pyörivät palvelut (daemonit) sekä palveluiden versiot hyökkäyspinnan hahmottamiseksi.
* **Oma huomio / idea:** Porttiskannauksessa kannattaa aina sovittaa skannausopeus (`-T` valitsin Nmapissa) ympäristön mukaan. Liian nopea skannaus voi pudottaa paketteja tai tukkia verkon, kun taas hitaampi skannaus auttaa välttämään havaituksi tulemisen.

### 4. KKO 2003:36
 **Tiivistelmä:**
 * Ratkaisussa käsiteltiin tietomurron tunnusmerkistöä ja luvattoman käytön rajoja Suomen rikoslain mukaan.
 * Suojauksen kiertäminen tai murtaminen ilman lupaa täyttää tietomurron tunnusmerkistön, vaikka järjestelmälle ei aiheutettaisi suoranaista vahinkoa.
 * **Oma huomio / idea:** Suomen laissa raja luvallisen testauksen ja rikoksen välillä on ehdoton. Kaikki penetraatiotestaus vaatii aina etukäteen sovitun kirjallisen luvan (Rules of Engagement) tai suljetun labraympäristön.


## a) Asenna Kali virtuaalikoneeseen
* Latasin ensimmäistä kertaa valmiin prebuilt-virtuaalikonekuvan manuaalisen ISO-asennuksen sijaan ja importtasin sen VirtualBoxiin. Kalin kotisivu josta latasin virtuaalikoneen:
```bash
    https://www.kali.org/get-kali/#kali-virtual-machines
```
Purkasin WinRar paketin, kanison sisältö:

<img width="645" height="124" alt="image" src="https://github.com/user-attachments/assets/46f8bd04-5310-45e6-97ed-e8c1900e3343" />

Pre-built on kätevä sillä että voin ohittaa uuden koneen luomisen ja suoraan avata virtuaalikoneen kansiosta tuplaklikkaamalla sitä, tai vaihoehtoisesti lisätä sen VirtualBoxissa itsessään Add -> Virtual Machine -> kali-linux-2026.2-virtualbox-amd64 -> start.
Kokeilen avata koneen suoraan kansiosta, koska en ole aiemmin näin tehnyt. Lisäsin koneen, Kali konfiguroi koneen näin:


<img width="536" height="561" alt="image" src="https://github.com/user-attachments/assets/9f2a26a7-131c-4265-b648-864fc9436151" />

<img width="408" height="126" alt="image" src="https://github.com/user-attachments/assets/2b088db6-636a-4923-bdf4-b22d5c558805" />



* Mielestäni ovat varanneet koneelle liian vähän rautaa, mutta kokeilen onnistuuko näillä ja toki kirjautumistunnukset olisi hyvä vaihtaa. Guest Addition:it toimii oikein ja kone ei vaikuta hitaalta :) näyttää olevan myös kaikki kali:n työkalut, tähän saakka pre-built on ollut todella kätevä.






### Miten prebuilt-kuva poikkeaa perus ISO-asennuksesta?
* Prebuilt VM (Valmis kuva):
* Vaivattomuus ja nopeus: Asennusvaihetta (kielen valinta, osiointi, käyttäjien luonti) ei tarvitse käydä läpi, vaan kone on käyttövalmis minuuteissa.
* Valmiit Guest Additions -lisäosat: VirtualBoxin integrointityökalut (esim. näytön resoluution automaattinen skaalautuvuus, leikepöydän jako ja hiiren saumaton käyttö) on esiasennettu ja konfiguroitu valmiiksi.

Yhteenveto: Prebuilt-kuva osoittautui erinomaiseksi ja erittäin nopeaksi ratkaisuksi laboratorioympäristöön, sillä se säästi aikaa itse tehtävien suorittamiseen ilman asennusvaiheen kikkailua. Asennuksessa ei ilmennyt ongelmia.
