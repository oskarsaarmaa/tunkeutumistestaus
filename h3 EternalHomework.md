## x)

### Jaswal 2020: Mastering Metasploit
Luvun 1 loppuosa käsittelee järjestelmällistä lähestymistapaa tunkeutumistestaukseen Metasploit Frameworkia (MSF) käyttäen.

* Tunkeutumistestaus ei ole satunnaista hyökkäysten kokeilua, vaan se jakautuu selkeisiin vaiheisiin:
  
  * Tiedonkeruu (Reconnaissance)
  * Skannaus (Scanning)
  * Haavoittuvuusanalyysi (Vulnerability Assessment)
  * Hyökkäys (Exploitation)
  * Hyökkäyksen jälkeiset toimet (Post-Exploitation).

* Metasploitista löytyy omia valmiita lisätyökaluja (auxiliary-moduuleja), joilla voi etsiä verkossa olevia laitteita, tarkistaa mitä palveluita niissä pyörii ja lukea tietokantoja. Tämän ansiosta kaikkea ei tarvitse tehdä muilla erillisillä ohjelmilla.
* Skannaustulosten tallentaminen PostgreSQL-tietokantaan mahdollistaa kohdejärjestelmien, avointen porttien ja haavoittuvuuksien tehokkaan hallinnan ja suodatuksen laajojakin verkkoympäristöjä testattaessa.
* Kun laitteet ja niiden ohjelmistoversiot on selvitetty, valitaan juuri oikea murtautumistyökalu (exploit) ja komentokanava (payload). Näin vältetään turhat virheet ja estetään kohdejärjestelmän kaatuminen.
* Kun murto onnistuu, Meterpreter avaa tehokkaan hallintakanavan suoraan tietokoneen keskusmuistiin ilman, että kovalevylle tarvitsee tallentaa tiedostoja. Sen avulla on helppo kerätä tietoa laitteesta ja liikkua eteenpäin muihin samassa verkossa oleviin koneisiin (pivoting).

Lähde: Mastering Metasploit - Fourth Edition: https://www.oreilly.com/library/view/mastering-metasploit/9781838980078/B15076_01_Final_ASB_ePub.xhtml#_idParaDest-31


### Mitä `nmap -sn` tekee 
Komento `nmap -sn` (No port scan) suorittaa ping-skannauksen (Host Discovery). Se määrittää, mitkä osoitteet ovat pystyssä (alive), ilman porttiskannausta.


Kun komento ajetaan paikallisessa Ethernet-verkossa (samassa aliverkossa / Layer 2) pääkäyttäjän oikeuksilla Nmap käyttää oletuksena ARP-pyyntöjä (`ARP request`). Tämä palauttaa nopeasti ja luotettavasti tiedon aktiivisista laitteista sekä niiden MAC-osoitteista, vaikka laitteiden palomuuri estäisi ICMP-pingit.
Jos kohde on eri aliverkossaTämä palauttaa nopeasti ja luotettavasti tiedon aktiivisista laitteista sekä niiden MAC-osoitteista, vaikka laitteiden palomuuri estäisi ICMP-pingit. Jos kohde on eri aliverkossa (Layer 3), Nmap lähettää ICMP Echo Request `TCP SYN` (porttiin 443) `TCP ACK` (porttiin 80) ja ICMP

#### Lähteet ja niiden luotettavuuden perustelu:
Nmap Official Documentation nmap.org:

* Lähde: Nmap Reference Guide – Host Discovery Options (`-sn`).  https://nmap.org/book/man-host-discovery.html
* Luotettavuus: Virallinen tuotedokumentaatio, jonka on kirjoittanut työkalun alkuperäinen kehittäjä Gordon Lyon. Dokumentaatio päivittyy jatkuvasti ja edustaa ensikäden lähdettä.

Nmap Man-sivu (`nmap man`):

* Lähde: Paikallisen Linux-järjestelmän virallinen ohjekirja.
* Luotettavuus: Käyttöjärjestelmäjakelun mukana toimitettava varmistettu ja muuttumaton tekninen dokumentaatio.



## a)
