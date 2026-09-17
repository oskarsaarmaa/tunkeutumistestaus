# Harjoitus 5: Salasanat ja niiden murtaminen

Harjoitus 5: Tunkeutumistestaus kurssin raportti 


## x)

### Karvinen 2022: Cracking Passwords with Hashcat
Lähde: [Tero Karvinen: Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)

* Hashcatin suorituskyky: Hashcat on maailman nopein salasananmurtaja, joka hyödyntää näytönohjainten (GPU) rinnakkaislaskentaa (CUDA / OpenCL) saavuttaen miljardeja tiivisteversioita sekunnissa riippuen algoritmin raskaudesta.
* Tunnistus ja tyypit (`-m` / Hash Type): Hashcat vaatii tiedon murtokohteen algoritmityypistä (esim. `-m 0` = MD5, `-m 1000` = NTLM, `-m 1800` = SHA-512 Unixcrypt).
* Peruskomento: Formaatti noudattaa kaavaa `hashcat -m <type> -a <attack_mode> <hash_file> <wordlist>`.
* Suorituskyvyn haasteet: Raskaat algoritmityypit (kuten `bcrypt` tai Linuxin uudempien jakeluiden `yescrypt`) sisältävät tietoisesti work factor / cost factor -viiveitä, jolloin GPU-kiihdytyksen hyöty pienenee merkittävästi.


### Karvinen 2023: Crack File Password With John
Lähde: [Tero Karvinen: Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)

* SALAUSformaattien muunnos (2john-työkalut) John the Ripper (JtR) ei pysty suoraan murtamaan monimutkaisia tiedostoformaatteja (ZIP, PDF, SSH-avaimet), vaan suojatusta tiedostosta on ensin erotettava salausavain ja tiivisteosuus ns. `*2john`-työkaluilla (esim. `zip2john`, `pdf2john`, `ssh2john`).
* JtR-joustavuus: John the Ripper tunnistaa automaattisesti useimmat tiivistemuodot ilman manuaalista tyyppimääritystä, mikä tekee siitä erinomaisen ensivaiheen työkalun.
* Sanalistat: Tehokkuus riippuu suoraan käytetystä sanakirjasta. Standardina laboratorioympäristöissä toimii SecListsin `rockyou.txt`.


### Santos et al. 2017: Security Penetration Testing - Lesson 6: Hacking User Credentials
Lähde: [Santos et al. 2017: Security Penetration Testing (O'Reilly Learning)](https://learning.oreilly.com/videos/security-penetration-testing/9780134833989/9780134833989-sptt_00_06_00_00/)

* Credential Harvesting: Käyttäjätunnusten ja salasanojen kaappaaminen on yksi verkon sisäisen tunkeutumisen (Internal Pentest) avainvaiheista.
* Offline vs Online Cracking: Online -hyökkäyksissä (brute force esim. SSH:ta tai HTTP Basic Authia vastaan) nopeusrajoitteena on verkko ja lukitusmekanismit. *Offline*-murtamisessa (kuten tässä harjoituksessa) hyökkääjällä on tiiviste tai salattu tiedosto omalla koneellaan, jolloin murtaminen tapahtuu paikallisen suorittimen/näytönohjaimen maksiminopeudella ilman verkkotaustaa tai tilintukkeutumisriskiä (Account Lockout Policy).


> Oma huomio / ajatus:

> Nykyaikainen tietoturvasuositus nojaa pitkiin lausepohjaisiin salasanoihin (passphrase) sekä monivaiheiseen tunnistautumiseen (MFA). Kuten lukumateriaaleista käy ilmi, offline-hyökkäyksissä lyhyetkin monimutkaiset salasanat murretaan GPU-teholla ja tehokkailla sääntötiedostoilla (`rules`) muutamissa minuuteissa, jos käytetty algoritmi on kevyt (kuten MD5 tai NTLM).




## a) Hashcat: Asennus ja esimerkkisalasanan murtaminen

Asennetaan Hashcat ja murretaan standardi MD5-tiiviste sanakirjahyökkäyksellä.

### Asennus

```bash
sudo apt-get update
sudo apt-get install -y hashcat
