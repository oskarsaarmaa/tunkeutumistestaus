 ## x) Lue / katso / kuuntele ja tiivistä
###  1. Podcast: Herrasmieshakkerit – Jakso 0x45: Erikoistilanteiden asiantuntija, vieraana Juhani Mäkinen (16.6.2026)
**Tiivistelmä:**
* Jaksossa käydään läpi Applen laitteistotason tietoturvaa, core crypto -kirjaston verifiointia ja macOS-kernelin muistinkorruptiohaavoittuvuuksia.
* Keskustelussa sivutaan myös kriisinhallintaa ja erikoisjoukkotoimintaa Afganistanissa (Liittyen Juhani Mäkisen *Kontakti*-kirjaan).
* Lisäksi jaksossa käsitellään Suomen Pelimuseon uudistusta ja Peter Steinbergerin TED-puheenvuoroa.
  
## 2. Hutchins et al. 2011: Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains
 **Tiivistelmä:**
 Artikkelissa käydään läpi Lockheed Martinin kehittämä *Intrusion Kill Chain* -malli, joka jakaa verkkohyökkäyksen 7 eri vaiheeseen:
* **1. Reconnaissance** (Tiedustelu ja kohteen valinta)
* **2. Weaponization** (Aseistaminen / hyökkäysvektorin luonti)
* **3. Delivery** (Toimitus kohteelle, esim. phishing)
* **4. Exploitation** (Aukon hyödyntäminen koodin suorittamiseksi)
* **5. Installation** (Haittaohjelman/haavoittuvuuden asentaminen koneelle)
* **6. Command & Control (C2)** (Etähallintayhteyden muodostaminen)
* **7. Actions on Objectives** (Tavoitteen toteutus, esim. datavarkaus tai tuho)
 * Puolustajan näkökulmasta oleellista on se, että hyökkääjän pitää onnistua jokaisessa 7 vaiheessa peräkkäin. Jos ketju saadaan katki missä tahansa kohtaa, hyökkäys epäonnistuu.

### 3. Santos et al.: The Art of Hacking – 4.3 Surveying Essential Tools for Active Reconnaissance
**Tiivistelmä:**
* Videokokonaisuudessa käydään läpi aktiivisen tiedustelun perustyökaluja, kuten Nmap, Masscan ja Netcat.
* Aktiivinen tiedustelu poikkeaa passiivisesta (OSINT) siinä, että se lähettää paketteja suoraan kohdejärjestelmään ja jättää aina jälkiä kohdekoneen tai palomuurin lokitietoihin.
* Porttiskannauksella selvitetään avoimet portit, niissä pyörivät palvelut (daemonit) sekä palveluiden versiot hyökkäyspinnan hahmottamiseksi.

### 4. KKO 2003:36
 **Tiivistelmä:**
 * Ratkaisussa käsiteltiin tietomurron tunnusmerkistöä ja luvattoman käytön rajoja Suomen rikoslain mukaan.
 * Suojauksen kiertäminen tai murtaminen ilman lupaa täyttää tietomurron tunnusmerkistön, vaikka järjestelmälle ei aiheutettaisi suoranaista vahinkoa.


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


## b) Verkkoyhteyden katkaiseminen

<img width="563" height="266" alt="image" src="https://github.com/user-attachments/assets/74433a6e-ff2d-464d-9f91-dfa5bfd1c1a2" />

```bash
# Asetusmuutos VirtualBoxissa:
# Settings -> Network -> Adapter 1 -> Attached to: "Not Attached"

# Yhteyden testaus Kalin terminaalissa:
```

<img width="359" height="60" alt="image" src="https://github.com/user-attachments/assets/35d51789-47de-4128-b3f1-a91816e38325" />



## c) Nmap localhost porttiskannaus  

<img width="847" height="208" alt="image" src="https://github.com/user-attachments/assets/2653cf1e-c57a-42d7-aed4-c838f22e3f34" />

Komento **sudo nmap -T4 -A localhost**
* -T4 (Timing template): Asettaa skannauksen aikaprofiiliksi tasolle 4/5 ("Aggressive"). Tämä nopeuttaa skannausta lyhentämällä viiveitä ja timeout-aikoja, mikä sopii hyvin nopeisiin lähiverkko- tai localhost-skannauksiin.
* -A (Aggressive scan option): Kytkee päälle useita kehittyneitä tunnistusominaisuuksia:
  *   Käyttöjärjestelmän tunnistus (OS detection, -O)
  * Palvelujen versiotunnistus (Version detection, -sV)
  * Skriptiskannaus (Script scanning, -sC / NSE-skriptit)
  * Traceroute
 
* Kohde **localhost**
* Jos porttien skannausaluetta ei erikseen määritetä Nmap skannaa 1000 yleisintä porttia kohdeosoitteesta tässä tapauksessa localhost:sta (127.0.0.1)

Komennon tulos:
* Skannauksen tuloksena kaikkien 1 000 skannatun portin tilaksi palautui closed.
* Portit ovat suljettuja, eli kone vastasi skannauspaketteihin, mutta mikään sovellus tai palvelu ei kuuntele näissä porteissa pyyntöjä.


## d) Demonien asennus ja uudelleenskannaus 
Päivitin koneen ja asensin kaksi demonia tuttu apache2 ja uusi vsftpd FTP palvelin:

```bash
sudo apt update
sudo apt install  apache2
sudo apt install  vsftpd
sudo systemctl start apache2 vsftpd
```

<img width="770" height="86" alt="image" src="https://github.com/user-attachments/assets/7de03c82-a8db-43dd-8d13-9dcf3b39886c" />

<img width="728" height="81" alt="image" src="https://github.com/user-attachments/assets/16baaddf-5d03-46ba-914c-e4f4294f096d" />

Uudellenskannaus:

```bash
sudo nmap -T4 -A localhost
```


<img width="2396" height="315" alt="image" src="https://github.com/user-attachments/assets/5ba5b227-7f39-4481-9507-f8a2f7323937" />


Skannauksen tulos:
* Avoimet portit: Skannaus havaitsi kaksi avointa porttia: 21/tcp (FTP) ja 80/tcp (HTTP)
* Nmap sai selville korkealla todennäköisyydellä, että skannattu kohde käyttää Linux käyttöjärjestelmää.
* Versiotunnistus: Nmap tunnisti portissa 21 pyörivän vsftpd 3.0.5 -palvelun ja portissa 80 Apache httpd 2.4.62 -web-palvelimen. Skanni sai myös selville apache2 oletussivun otsikon (Debian's Apache2 Default Page).
* Ero c-kohtaan: Ennen palveluiden asentamista kaikkien 1 000 portin tila oli closed. Palveluiden käynnistämisen jälkeen portit 21 ja 80 avattiin palveluiden pyörittämisen syystä, Nmap kykeni tunnistamaan sekä avoimet portit että niissä pyörivät sovellusversiot.

## e) Hack The Box tehtävä: Fawn

### Ratkaisin OpenVPN-yhteydellä Hack The Boxin **Fawn** tehtävän. 

<img width="1437" height="606" alt="image" src="https://github.com/user-attachments/assets/e13d456b-a5a7-4ef2-9b58-3182772acffa" />

### VPN-yhteys ja Ping-testi:
* Loin yhteyden Hack The Box verkkoon latamalla .ovpn profiilini ja ajamalla komento:
  
<img width="460" height="581" alt="image" src="https://github.com/user-attachments/assets/493cb489-3335-4f75-9663-ac60305ec896" />

<img width="387" height="65" alt="image" src="https://github.com/user-attachments/assets/627ee630-9a5e-46af-90a6-5193c0927b7d" />


  ```bash
  sudo openvpn starting_points_eu-starting-point-2-dhcp.ovpn
  ```
<img width="970" height="1071" alt="image" src="https://github.com/user-attachments/assets/d9a87ff5-50e1-49f9-8878-0018cad33e19" />


* Nyt olen yhdistäytynyt Hack The Box verkkoon OpenVPN avulla.


Testaan saanko yhteyttä Fawn koneseen **ping** komennon avulla:

<img width="565" height="187" alt="image" src="https://github.com/user-attachments/assets/297bd614-e0ff-402b-ab95-6db35783d01a" />

Fawn koneseen kohdistuva Nmap skanni -sV lipun avulla, lippu skannaa avoimet portit ja palvelut kohdeosoitteessa + ottaa selvää millä versiolla palvelut pyörivät ja mahdollisesti minkä käyttöjärjestelmällä kone pyörii:


### Nmap skannaus
```bash
sudo nmap -sV 10.129.176.75

```

<img width="782" height="203" alt="image" src="https://github.com/user-attachments/assets/03f999e9-30ec-44c7-b05f-139148e4b1fd" />

* Skannista tuloksesta huomaa, että portti 21 on auki ja siinä pyörii vsftpd (ftp) palvelu/demoni, ja pyörii Unix:lla.

  
### FTP kirjautuminen 
Hack The Box tehtävänanto vihjasi FTP-protokollan perusominaisuuksista, ja pienen googlailun jälkeen sain tietää että FTP-palvelimien oletustunnus on **anonymous** ja Fawn FTP-palvelimella ei ollut salasanaa määritetty ollenkaan pääsi kirjautumaan **Entetiä** painamalla ilmesty koodi: 230 kirjautuminen meni läpi.
Otin yhteyttä FTP palvelimelle: 

```bash
ftp 10.129.176.75
Name: anonymous
Password: - (enter)
ls -> näkyy flag.txt
get flag.txt
exit
```

<img width="1249" height="390" alt="image" src="https://github.com/user-attachments/assets/97dd1ee2-8f75-40c3-be65-00f490ab7cc6" />

Omalla koneella luettiin tiedoston sisältö cat flag.txt -komennolla, josta saatiin tehtävän lippu: 035db21c881520061c53e0536e44f815.

<img width="278" height="56" alt="image" src="https://github.com/user-attachments/assets/99e55b83-5b9f-42fc-b184-d7a41181978e" />

