## x) Lue/katso ja tiivistä

### OWASP 2021: OWASP Top 10:2021

* Tiivistelmä:
  * Broken Access Control nousee vuoden 2021 OWASP Top 10 -listan kärkisijalle (A01). Se tarkoittaa tilannetta, jossa järjestelmä ei valvo käyttöoikeuksia riittävän tiukasti, jolloin käyttäjä pääsee käsiksi tietoihin tai toimintoihin, joihin hänellä ei pitäisi olla valtuuksia.
  * Haavoittuvuus kattaa useita tunnettuja hyökkäysvektoreita, kuten IDOR (Insecure Direct Object References), Path Traversal, CORS-virhekonfiguraatiot sekä käyttöoikeuksien ohittamisen muokkaamalla URL-osoitetta tai HTTP-pyynnön parametreja.
  * Korjaamiseen suositellaan "deny by default" -periaatetta, keskitettyä käyttöoikeuksien tarkistuslogiikkaa palvelinpuolella sekä kaikkien suorien objektiviitteiden korvaamista epäsuorilla/satunnaisilla tunnisteilla (GUID/UUID).
 
* Oma huomio:
  
    * Infrastruktuurin näkökulmasta on mielenkiintoista, miten usein käyttöoikeustarkistukset jätetään pelkän käyttöliittymän (frontend) varaan. Jos API-rajapinta ei validoi pyyntöä tekevää sessiota suhteessa pyydettyyn resurssiin palvelimella, mikään määrä piilotettuja nappeja UI:ssa ei suojaa järjestelmää.

### PortSwigger Academy: Insecure Direct Object References (IDOR)

* Tiivistelmä:
  * IDOR on Broken Access Controlin alalaji, jossa sovellus käyttää käyttäjän syötettä (esim. parametria `?user_id=102`) suoraan tietokantakyselyssä tai tiedostojärjestelmässä ilman, että pyynnön tekijän oikeuksia varmistetaan.
  * Hyökkääjä voi vaihtaa parametrin arvoa (`?user_id=103`) ja päästä käsiksi toisen käyttäjän yksityisiin tietoihin, kuten laskuihin, profiilitietoihin tai chat-lokitiedostoihin.

* Oma huomio:
  
    * IDOR on konseptina erittäin yksinkertainen, mutta sen automaattinen skannaus on vaikeaa, koska skanneri ei aina tiedä, mikä parametri on tarkoitettu julkiseksi ja mikä luottamukselliseksi. Tämä tekee siitä erinomaisen kohteen manuaaliselle testaukselle.

## PortSwigger Academy: Path Traversal

* Tiivistelmä:

  * Path traversal (tai directory traversal) mahdollistaa hyökkääjälle mielivaltaisten tiedostojen lukemisen palvelimen tiedostojärjestelmästä.
  * Haavoittuvuus syntyy, kun sovellus ottaa tiedostonimen syötteenä (esim. `filename=kuva.png`) ja liittää sen tiedostopolkuun ilman sanitointia.
  * Suhteellisten polkujen kiipeäminen perustuu `../` sekvensseihin, joilla päästään pois sovelluksen juurihakemistosta (esim. `/var/www/html/`) palvelimen järjestelmähakemistoihin (esim. `/etc/passwd`).
    
* Oma huomio:
  
    * Miten nykyaikaiset konttiympäristöt (Docker/Podman) vaikuttavat Path Traversalin vakavuuteen? Vaikka hyökkääjä pääsisi lukemaan `/etc/passwd` tiedoston, hän lukee vain isolated container ympäristön tiedostoa, ei isäntäjärjestelmän (host).


### PortSwigger Academy: Cross-Site Scripting (XSS)

* Tiivistelmä:

  * XSS-haavoittuvuudessa hyökkääjä syöttää haitallista JavaScript-koodia verkkosivulle, ja uhrin selain suorittaa sen luottaen sivustoon.
  * Jaetaan kolmeen päätyyppiin:
    
    * Reflected XSS: Haitallinen skripti tulee HTTP-pyynnön mukana (esim. hakukentän parametrisuosikkilinkissä) ja heijastuu välittömästi takaisin vastauksessa.
    * Stored XSS: Haitallinen kripte tallennetaan tietokantaan (esim. kommenttikenttä tai profiilikuvaus), josta se latautuu kaikille sivua katsoville käyttäjille.
    * DOM-based XSS: Haavoittuvuus on täysin asiakaspuolen (client-side) JavaScript-koodissa, joka käsittelee epäluotettavaa syötettä turvattomasti.

* Oma huomio:
  
    * Monesti XSS demonstroidaan `alert()` funktiolla, mutta todellisessa hyökkäyksessä vaara liittyy istuntokaappaukseen (Session Hijacking via `document.cookie`) tai näkymättömään tiedonkalasteluun samalla domainilla.


 

### Lähteet:

Vinkit ja tehtävä itsessään: https://terokarvinen.com/tunkeutumistestaus/


 ## a) Totally Legit Certificate – OWASP ZAP & CA-Sertifikaatin Asennus

 ### Tavoite

 Asentaa OWASP ZAP Kali Linuxiin, generoida ZAP:n CA-sertifikaatti, tuoda se Firefox-selaimeen ja reitittää selaimen liikenne ZAP-proxyn läpi. Varmistetaan, että myös kuva-informaatio siepataan.

Päivitettiin Kali Linuxin pakettilistat (`sudo apt-get update`) ja asennettiin ZAP-proxy (`sudo apt-get install -y zaproxy`).

 <img width="849" height="562" alt="image" src="https://github.com/user-attachments/assets/8e221909-f858-43d7-924c-ca212dc8d7a0" />
 

Sertifikaatin luonti ja vienti: Generoitiin OWASP ZAPissa uusi juurisertifikaatti (Dynamic SSL Certificate) ja tallennettiin se järjestelmään nimellä `zap_root_ca.cer`.


<img width="1120" height="639" alt="image" src="https://github.com/user-attachments/assets/116e9019-cee6-4ed1-b139-b794973cd138" />

 
 <img width="682" height="362" alt="image" src="https://github.com/user-attachments/assets/ee8f278a-852b-479b-a938-55640433634b" />



CA-sertifikaatin luottamus: Tuotiin generoitu sertifikaatti Firefox-selaimen sertifikaattihallintaan (Authorities-välilehdelle) ja asetettiin selain luottamaan siihen verkkosivustojen tunnistamisessa.


<img width="740" height="153" alt="image" src="https://github.com/user-attachments/assets/008fdcf7-cb54-453c-9a16-ebc8b57df680" />


Valitaan luotu ZAP sertifikaatti

<img width="771" height="312" alt="image" src="https://github.com/user-attachments/assets/5870e973-b82f-4456-8a96-db6cf52e6ace" />


Näkyy Firefox sertifikaateissa

<img width="672" height="462" alt="image" src="https://github.com/user-attachments/assets/e5b7d7a1-5c28-4a42-bc6f-655e1e66144d" />



Näen asennetun kuvan `http://localhost:8000/testi.png`

<img width="1216" height="775" alt="image" src="https://github.com/user-attachments/assets/90a1c3e3-ff7e-4cca-8180-cb1199bdf5da" />



Liikenteen varmentaminen ja kaappaus: Käynnistettiin paikallinen HTTP-palvelin porttiin 8000 ja ohjattiin Firefoxin liikenne ZAP-proxyn läpi (portti 8080). Testikuvapyyntö (`http://localhost:8000/testi.png`) saatiin kaapattua onnistuneesti ZAPiin, mikä näkyy suoraan ZAPin Sites-puussa ja pyynnön otsaketiedoissa (Request).

 <img width="1899" height="881" alt="image" src="https://github.com/user-attachments/assets/bd98f61d-17e4-40fe-a44b-6a97718f810c" />

 
