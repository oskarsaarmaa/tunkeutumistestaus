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


# Tarkistetaan järjestelmän suorituskyky ja tunnistetut laitteet
hashcat -I

```



Tarkistan järjestelmän suorituskyky ja tunnistetut laitteet (CPU/GPU) komennolla `hashcat -I`


<details>
<summary>Testitulos</summary>

<img width="593" height="318" alt="image" src="https://github.com/user-attachments/assets/5b1c28cf-6e5a-4562-8c4e-c53ec0bc77cf" />


</details>

* Virheen syy: Hashcat tarvitsee OpenCL-, CUDA- tai HIP-laskenta-alustan, jota järjestelmästä ei aluksi löytynyt (esim. virtuaalikoneesta puuttuvan GPU-ajurin vuoksi).
* Miksi se ei toiminut: Ilman yhteensopivaa ajuria Hashcat ei osannut kääntää eikä siirtää rinnakkaislaskennan koodia laitteistosi käsiteltäväksi.
* Miksi se toimii nyt: Asennettu `pocl-opencl-icd` paketti toimii tulkkina, joka kääntää Hashcatin OpenCL-koodin lennosta prosessorisi (CPU) omiksi x86-konekieliohjeiksi.
* Nykytila: PoCL esittää prosessorisi Hashcatille OpenCL-yhteensopivana laskentalaitteena, jolloin ohjelma pystyy hyödyntämään CPU:n kaikkia ytimiä salasanatiivisteiden murtamiseen.


<details>
<summary>Testitulos</summary>

<img width="929" height="447" alt="image" src="https://github.com/user-attachments/assets/1c1a2dfa-e8bd-4546-a21f-f043d83d07de" />


</details>


### Testaus MD5-tiivisteen murtaminen

Luodaan testitiiviste. MD5-tiiviste sanalle `kekskeksi` on `26e25721113b6b1580231920cf67210e`.

```bash
# Tallennetaan tiiviste tiedostoon
echo "26e25721113b6b1580231920cf67210e" > target_hash.txt

# Luodaan pieni testaussanakirja
echo -e "password\n123456\nkekskeksi\nadmin" > test_words.txt

# Ajetaan Hashcat (Mode -m 0 = MD5, -a 0 = Straight/Dictionary)
hashcat -m 0 -a 0 target_hash.txt test_words.txt

```

<details>
<summary>Tiedostojen luonti: </summary>

<img width="1392" height="160" alt="image" src="https://github.com/user-attachments/assets/84ff1997-d0a6-4dea-9afd-987aab8d4fcc" />


</details>



<details>
<summary>Exhausted: </summary>

<img width="1210" height="1124" alt="image" src="https://github.com/user-attachments/assets/527385d0-e9ea-48c1-8b05-8fbb02997548" />



</details>


* Testin tulokseksi tuli Exhausted joka tarkoittaa sitä, että Hashcat kävi läpi koko sanakirjan jokaisen sanan (tässä tapauksessa 4 sanaa), mutta oikeaa salasanaa ei löytynyt kyseisestä sanalistasta.
* `Recovered........: 0/1 (0.00%)` vahvistaa sen ettei tiivistetä saatu murrettua.
* Näin kävi, koska sanalistassani `test_words.txt oli 4 sanaa (Passwords..: 4 ja ehdokkaat password -> admin)`. Jos `target_hash.txt` tiedostossa oleva tiiviste on laskettu jostakin muusta sanasta (esim. `keksiteksti`) Hashcat vertaa tiivistettä vain niihin 4 sanaan eikä löydä osumaa.
* Cracked tilan saan saavutettua siten, että lisään murtamani sanan sanakirjaan:


<details>
<summary>Sanakirjojen muokkaus: </summary>

<img width="598" height="108" alt="image" src="https://github.com/user-attachments/assets/81695fc6-1353-40e4-aefa-613c48d7d3af" />


</details>


* `echo -n "kekskeksi"`: `echo` tulostaa merkkijonon. Lippu `-n` (no newline) estää rivinvaihtomerkin (`\n`) lisäämisen merkkijonon perään. Tämä on kriittistä, sillä muuten tiiviste laskettaisiin merkkijonolle `"kekskeksi\n"`, mikä antaisi täysin eri MD5-arvon.
* `|` (putki): Ohjaa vasemmanpuoleisen komennon tulosteen suoraan oikealla olevan komennon syötteeksi.
* `md5sum`: Laskee syötteestä 128-bittisen MD5-tiivisteen.
* `cut -d' ' -f1`: Pilkkoo `md5sum`-komennon tulosteen. Lippu `-d' '` määrittää erottimeksi välilyönnin ja `-f1` valitsee ensimmäisen kentän (eli pelkän tiivisteen), jolloin tulosteesta poistetaan sen perään tulostuva viiva `-`.
* `>`: Ohjaa tulosteen tiedostoon (ja ylikirjoittaa tiedoston, jos se on jo olemassa).
* `echo -e`: Lippu `-e` mahdollistaa erikoismerkkien (kuten `\n` = uusi rivi) tulkkaamisen, jolloin sanakirjaan saadaan jokainen sana omalle rivilleen.
* `hashcat -m 0`: Määrittää murtokohteen tiivisteosio/algoritmin tyypin (Hash Type). `0` tarkoittaa perus MD5-tiivistettä.
* `hashcat -a 0`: Määrittää hyökkäysmuodon (Attack Mode). `0` tarkoittaa suoraa sanakirjahyökkäystä (Straight / Dictionary attack).
  

<details>
<summary>Cracked: </summary>

<img width="1201" height="1135" alt="image" src="https://github.com/user-attachments/assets/d12b5ae5-4bdb-4d9f-8dbd-f14d986c7767" />



</details>


