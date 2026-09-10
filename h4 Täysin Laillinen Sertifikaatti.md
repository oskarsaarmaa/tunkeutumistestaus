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


 


 ## a) Totally Legit Sertificate
 
