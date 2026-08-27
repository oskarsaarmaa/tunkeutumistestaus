## x) Lue/katso/kuuntele ja tiivistä.

### Buuri 2026
Punaisen tiimin (Red Team) toiminta on siirtynyt "villistä lännestä" ja maanalaisesta taiteesta tarkkaan säänneltyyn ja standardoituun toimintaan. 

Uhkaperusteisuus (TLPT): Testaus ei ole satunnaista haavoittuvuusskannausta, vaan se pohjautuu reaaliseen uhkatietoon (Threat Intelligence), jolla mallinnetaan juuri kyseiseen organisaatioon kohdistuvia todellisia hyökkääjiä (APT-ryhmät = Advanced Persistant Threat).

Sääntelypaine: EU:n DORA-asetus tekee aiemmin vapaaehtoisesta TIBER-pohjaisesta testauksesta pakollista tietyille finanssialan toimijoille.

Lähde: https://terokarvinen.com/buuri-2026-dora-and-threat-lead-penetration-testing/buuri-2026-dora-and-threat-lead-penetration-testing--teros-pentest-course.pdf

### DORA

Artikla 26 (Advanced testing based on TLPT): Edellyttää finanssilaitoksia suorittamaan uhkaperusteisen tunkeutumistestauksen vähintään joka 3. vuosi. Testauksen tulee kattaa kriittiset toiminnot ja järjestelmät, mukaan lukien kolmannen osapuolen (ICT-palveluntarjoajien) tuottamat palvelut.


Artikla 27 (Requirements for testers): Asettaa tiukat vaatimukset testaajille: heillä on oltava riittävä riippumattomuus, sertifioitu ammattitaito, vähintään 3 vuoden kokemus uhkatieto- ja punatiimitoiminnasta sekä riittävä vastuuvakuutus. Ulkopuolisia testaajia on käytettävä säännöllisesti.


Lähde: https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng


### TIBER-FI

Yleiskuva: Red Team -testausvaihe on TIBER-FI-prosessin ydinvaihe, jossa hyökkäystiimi (Red Team) pyrkii saavuttamaan ennalta määritellyt kohteet (Target Objectives) käyttäen todellisia hyökkäystekniikoita ilman, että kohteen puolustus organisaatiossa (Blue Team) tietää testistä etukäteen.


Kontrolloitu vaara: Testaus vaatii jatkuvaa hallintaa ja riskien minimointia. Mukana on White Team (organisaation sisäinen valvontaryhmä), joka valvoo testiä ja voi keskeyttää sen, jos liiketoiminta vaarantuu.

Lähde: https://www.suomenpankki.fi/globalassets/bof/en/money-and-payments/the-bank-of-finland-as-catalyst-payments-council/tiber-fi/tiber-fi-2.0-procedures-and-guidelines.pdf

### Havainto
On kiinnostavaa nähdä miten kyberturva kehittyy hurjaa vauhtia pysytäksemme hyökkääjää edellä vaaditaan kokonaisvaltaista resilienssiä, jossa testataan myös ihmisiä, prosesseja ja incident response -valmiutta. Luennossa ilmi tulleet tekoälyä hyödyntävät hyökkäymenetelmät kuulostivat todella mielenkiintoisilta ja tehokkailta.


## a) Virtuaalikoneen Metasploitable 2 asennus

Latasin Metasploitable koneen:

```bash
    https://sourceforge.net/projects/metasploitable/
```
Konetta on viimeksi päivitetty 19.8.2019
```bash
login: msfadmin
password: msfadmin
```

## b) Kalin ja Metasploitablen välinen virtuaaliverkko

### Virtual Box:in virtuaaliverkon rakenne:

<img width="732" height="625" alt="image" src="https://github.com/user-attachments/assets/c3d275a1-0dd6-446d-9fa0-435a78071302" />

Kuva piirretty: https://asciiflow.com/#/

### Toisen verkkokortin lisäys ja konfigurointi
* Lisäsin Kali koneseen toisen verkkokortin `Adapter 2` kytkin sen `Host-Only Adapter` tilaan
    * Verkkokortti sai nimen: `eth1` ja osoitteen `192.168.56.101/24`
* `Host-Only Adapter` Virtual Box:ssa luo eristetyn virtuaaliverkon isäntäkoneen ja virtuaalikoneiden välille, tämä mahdollistaa turvallisen keskinäisen tietoliikenteen ilman suoraa pääsyä julkiseen internetiin tai fyysiseen lähiverkkoon.

* Kali Linux – NAT-yhteyden katkaisu
  * Kalin ensimmäiseltä verkkokortilta `Adapter 1` otin ruksin pois kohdasta `Virtual Cable Connected`
  * Käyttötarkoitus: Eristää Kali kone täysin ulkoisesta internetistä testausken ajaksi, jotta skannaukset ja hyökkäykseen liittyvä liikenne ei pääse vuotamaan pois testiympäristöstä.
    
* Metasploitable 2
   * Määritin koneen verkkokortin `Host-Only Adapter` tilaan ja jätin täplän `Virtual Cable Connected` asetukseen
   * Verkkokortti sai nimen `eth0` ja osoitteen `192.168.56.102/24`
* Maalikone on eristetty samaan verkkoon Kali koneen kanssa

Yhteenveto:

Loin täysin suljetun ja turvallisen Host-Only testiympäristön, tämä mahdollistaa nmap-skannaukset ja hyökkäystestit turvallisesti ilman riskiä liikenteen vuotamisesta ulkoverkkoon.

Lähde:
https://www.virtualbox.org/manual/ch06.html

## c) Kali ja Metasploitable koneiden IP-osoitteet ja yhteyden testaaminen

Varmistin virtuaalikoneiden verkkokorttien IP-osoitteet ajamalla molemmissa koneissa `ip address` komennolla:

* **Kali Linux (`eth1`):** `192.168.56.101/24`
<img width="838" height="265" alt="image" src="https://github.com/user-attachments/assets/34165f80-ba81-49b4-9956-15db371b60fa" />



  
* **Metasploitable 2 (`eth0`):** `192.168.56.102/24`
<img width="719" height="182" alt="image" src="https://github.com/user-attachments/assets/e3527ec9-c765-4657-89ff-faa467ccdbf4" />





Verkkoyhteyden testaamiseen pingaan Kali koneelta (`192.168.56.101/24`) Metasploit konetta (`192.168.56.102/24`):


<img width="317" height="70" alt="image" src="https://github.com/user-attachments/assets/6db4da15-fa81-4c7e-a029-504f76de810c" />

* Kali kone ei ole yhteydessä internettiin!

Kali -> Metasploitable

<img width="517" height="190" alt="image" src="https://github.com/user-attachments/assets/c8b329f4-82dc-4954-9cfb-1cb30e32a259" />



Metasploitable -> kali

<img width="606" height="165" alt="image" src="https://github.com/user-attachments/assets/e5d8e139-0f5d-45e2-920a-8119bd7f3f78" />



Yhteenveto:

Ping komennot menee läpi molempiin suuntiin, ja on verkko on kokonaan eristetty.







  
