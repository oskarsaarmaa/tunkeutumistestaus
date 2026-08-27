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


Lähde: https://sourceforge.net/projects/metasploitable/
