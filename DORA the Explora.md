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

## d) Nmap skannaus Metasploitale2 koneesta

Skannasin kohdekoneen (Metasplotable2) komennolla: `nmap -sN 192.168.56.102`. Tulos:

<img width="622" height="549" alt="image" src="https://github.com/user-attachments/assets/65a3ef61-4450-43b7-a6ed-71addcfc5a2b" />

### Nmap -sN (TCP Null Scan) -skannauksen analyysi:
* `-sN` lipun toimintaperiaate: Skannaus lähettää maalikoneeseen TCP-paketteja, joissa yksikään ohjauslippu (kuten SYN, ACK tai FIN) ei ole päällä (Null Scan).
* Ohjausliput:
    * `SYN`: (Synchronize) on yhteyden avaava kutsu. Tätä käytetään TCP-yhteydenottokättelyn ensimmäisessä vaiheessa.
    * `ACK`: (Acknowledge) on pakettien kuittaus. Vahvistaa, että vastaanottaja sai edellisen paketin perille.
    * `FIN`: (Finish) on yhteyden hallittu lopetus. . Käytetään, kun tiedonsiirto on valmis ja halutaan sulkea kanava.
    * `Null Scan`: On skannaus, jossa maalikoneeseen lähetetään "tyhjä" TCP-paketti ilman yhtäkään edellä mainituista ohjauslipuista.
* Miksi tila on `open|filtered`:
    * Jos portti on suljettu, kone vastaa heti takaisin "RST" (yhteys hylätty).
    * Jos portti taas on auki, se vain ihmettelee tyhjää pakettia eikä vastaa siihen mitään.
    * Koska vastattomuus voi johtua myös siitä, että välissä oleva palomuuri blokkasi paketin kokonaan, Nmap ei voi tietää varmaksi, onko portti oikeasti auki vai tilanteessa välissä palomuuri. Siksi se ilmoittaa tilaksi `open|filtered`.

* `-sN` lippu vs. `-sV` lippu:
    * `-sN` (Null Scan): Lähettää tyhjiä TCP-paketteja ilman lippuja. Se yrittää selvittää avoimet portit huomaamattomasti, mutta ilmoittaa avoimet portit epävarmoina muodossa `open|filtered` eikä tunnista sovellusversioita.
    * `-sV` (Version Scan): Skannaa avoimet portit ja kättelee palveluiden kanssa tunnistaakseen niissä pyörivät tärkeät sovellukset sekä niiden tarkat versiot. `-sV` lipun kanssa skannateessa on isompi mahdollisuus jäädä kiinni verrattuna `-sN` lipun kanssa skannamiseen.
  



Metasploitable2 webbipalvelimen etusivu osoitteessa: `http://192.168.56.102`

<img width="575" height="524" alt="image" src="https://github.com/user-attachments/assets/88890d1d-78be-4703-8dd6-63dc45b05a09" />



## e) Metasploitable koneen kaikkien porttien skannaus
Ajoin Metasploitable 2 maalikoneeseen kattavan Nmap skannauksen komennolla:
`nmap -A -T4 -p- 192.168.56.102`
* `-p-` Ajaa skannauksen kaikille mahdollisille TCP-porteille (1-65535)
* `-T4` Lippu asettaa skannauksen agressiviseksi, tämä tekee skannauksesta nopeamman.
* `-A` Yhdistää useita keskeisiä Nmap-ominaisuuksia kerralla:
    * OS Detection (`-O`): Tunnistaa maalikoneen käyttöjärjestelmän
    * Service Version Detection (`-sV`): Selvittää avointen porttien sovellukset ja niiden versiot.
    * Script Scanning (`-sC`): Ajaa Nmap Scripting Engine:n oletusskriptit avoimille porteille havaitakseen perustason haavoittuvuuksia ja lisätietoja.


<details>
<summary> Nmap-skannauksen tulos (nmap -A -T4 -p-):</summary>

```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-28 04:57 -0400
Nmap scan report for 192.168.56.102
Host is up (0.0035s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE         SERVICE
21/tcp   open|filtered ftp
22/tcp   open|filtered ssh
23/tcp   open|filtered telnet
25/tcp   open|filtered smtp
53/tcp   open|filtered domain
80/tcp   open|filtered http
111/tcp  open|filtered rpcbind
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
512/tcp  open|filtered exec
513/tcp  open|filtered login
514/tcp  open|filtered shell
1099/tcp open|filtered rmiregistry
1524/tcp open|filtered ingreslock
2049/tcp open|filtered nfs
2121/tcp open|filtered ccproxy-ftp
3306/tcp open|filtered mysql
5432/tcp open|filtered postgresql
5900/tcp open|filtered vnc
6000/tcp open|filtered X11
6667/tcp open|filtered irc
8009/tcp open|filtered ajp13
8180/tcp open|filtered unknown
MAC Address: 08:00:27:67:E3:A5 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 2.04 seconds
                                                                                                                                                                              
┌──(kali㉿kali)-[~/Desktop]
└─$ nmap -A -T4 -p- 192.168.56.102
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-28 05:47 -0400
Stats: 0:00:47 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 96.67% done; ETC: 05:48 (0:00:01 remaining)
Nmap scan report for 192.168.56.102
Host is up (0.0025s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
21/tcp    open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.56.101
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp    open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
23/tcp    open  telnet      Linux telnetd
25/tcp    open  smtp        Postfix smtpd
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
|_ssl-date: 2026-08-28T09:20:41+00:00; -29m17s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
53/tcp    open  domain      ISC BIND 9.4.2
| dns-nsid: 
|_  bind.version: 9.4.2
80/tcp    open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-title: Metasploitable2 - Linux
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      34599/udp   mountd
|   100005  1,2,3      43872/tcp   mountd
|   100021  1,3,4      52603/udp   nlockmgr
|   100021  1,3,4      54112/tcp   nlockmgr
|   100024  1          33619/tcp   status
|_  100024  1          56899/udp   status
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
512/tcp   open  exec        netkit-rsh rexecd
513/tcp   open  login
514/tcp   open  shell       Netkit rshd
1099/tcp  open  java-rmi    GNU Classpath grmiregistry
1524/tcp  open  bindshell   Metasploitable root shell
2049/tcp  open  nfs         2-4 (RPC #100003)
2121/tcp  open  ftp         ProFTPD 1.3.1
3306/tcp  open  mysql       MySQL 5.0.51a-3ubuntu5
| mysql-info: 
|   Protocol: 10
|   Version: 5.0.51a-3ubuntu5
|   Thread ID: 9
|   Capabilities flags: 43564
|   Some Capabilities: Support41Auth, LongColumnFlag, SupportsCompression, SupportsTransactions, Speaks41ProtocolNew, ConnectWithDatabase, SwitchToSSLAfterHandshake
|   Status: Autocommit
|_  Salt: fRIUPo^hs'dEkm^q?&?I
3632/tcp  open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
|_ssl-date: 2026-08-28T09:20:41+00:00; -29m17s from scanner time.
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
5900/tcp  open  vnc         VNC (protocol 3.3)
| vnc-info: 
|   Protocol version: 3.3
|   Security types: 
|_    VNC Authentication (2)
6000/tcp  open  X11         (access denied)
6667/tcp  open  irc         UnrealIRCd
6697/tcp  open  irc         UnrealIRCd
8009/tcp  open  ajp13       Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8180/tcp  open  http        Apache Tomcat/Coyote JSP engine 1.1
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/5.5
|_http-favicon: Apache Tomcat
8787/tcp  open  drb         Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
33619/tcp open  status      1 (RPC #100024)
43872/tcp open  mountd      1-3 (RPC #100005)
54112/tcp open  nlockmgr    1-4 (RPC #100021)
55781/tcp open  java-rmi    GNU Classpath grmiregistry
MAC Address: 08:00:27:67:E3:A5 (Oracle VirtualBox virtual NIC)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=8/28%OT=21%CT=1%CU=36156%PV=Y%DS=1%DC=D%G=Y%M=080027%T
OS:M=6A9159C6%P=x86_64-pc-linux-gnu)SEQ(SP=C6%GCD=1%ISR=CD%TI=Z%CI=Z%II=I%T
OS:S=6)SEQ(SP=C7%GCD=1%ISR=C6%TI=Z%CI=Z%II=I%TS=6)SEQ(SP=CB%GCD=1%ISR=CF%TI
OS:=Z%CI=Z%II=I%TS=6)SEQ(SP=CC%GCD=1%ISR=CD%TI=Z%CI=Z%II=I%TS=6)SEQ(SP=CC%G
OS:CD=1%ISR=D7%TI=Z%CI=Z%II=I%TS=6)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW7%O3=M5B
OS:4NNT11NW7%O4=M5B4ST11NW7%O5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=16A0%W2=16A0%
OS:W3=16A0%W4=16A0%W5=16A0%W6=16A0)ECN(R=Y%DF=Y%T=40%W=16D0%O=M5B4NNSNW7%CC
OS:=N%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=Y%DF=Y%T=40%W=1
OS:6A0%S=O%A=S+%F=AS%O=M5B4ST11NW7%RD=0%Q=)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=
OS:40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0
OS:%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R
OS:=Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: mean: 30m44s, deviation: 2h00m01s, median: -29m17s
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2026-08-28T05:20:36-04:00

TRACEROUTE
HOP RTT     ADDRESS
1   2.52 ms 192.168.56.102

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 163.36 seconds
 ``` 

</details>

### Hyökkäjille kiinnostavia portteja 
Portti 21/TCP FTP 
* Palvelu ja versio: `vsftpd 2.3.4`
* Miksi portti on kiinnostava hyökkääjälle: Skannista saadaan selville, että kone käyttää vsftpd 2.3.4 versioa, joka tunnetaan yhdestä tietoturvahistorian kuuluisimmasta takaporttihaavoittuvuudesta (CVE-2011-2523)
* Vaikutus: Jos käyttäjätunnuksen perään syöttää hymiön `:)` esim. `user test :)` hyökkääjä/kirjautuja saa salaamattoman root-tason komentorivin, jossa voi tehdä muutoksia ilman salasana kysyntöjä.
Lähde: https://nvd.nist.gov/vuln/detail/cve-2011-2523

Portti 6667/TCP 
* Palvelu ja versio: `UnrealIRCd 3.2.8.1`
* Miksi portti on kiinnostava hyökkääjälle: Porttilla pyörivän palvelun jakeluversioon on takaovi (CVE-2010-2075), joka oli voimassa 11/2009 – 06/2010.
* Vaikutus: Lähettelemällä merkkijonon, joka alkaa sanoilla `AB;` IRC porttiin 6667 järjestelm' suorittaa minkä tahansa hyökkääjän antaman komennon root oikeuksilla.
Lähde: https://www.cve.org/CVERecord?id=CVE-2010-2075

Portti 3632 TCP 
* Palvelu ja versio: `distccd v1 (GNU C/C++ compiler daemon)`
* Miksi portti on kiinnostava hyökkääjälle: `distcc` on työkalu, joka hajauttaa koodin kääntämisen verkon yli useille koneille prosessointitehon lisäämiseksi. Palvelu kärsii konfiguraatiovirheestä ja puuttuvasta todennuksesta (CVE-2004-2687).
* Vaikutus: Oletusasetuksilla ilman konfiguraatiota `distcc` hyväksyy minkä tahansa käännöskennokseen lähetetyn komennon ilman minkäänlaista käyttäjän tunnistautumista.
Lähde: https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2004-2687

## Lähteet: 
x)

a)

b)

c)

d)

Nmap Network Scanning (Official Guide): Lyon, G. (2009). Nmap Network Scanning: The Official Nmap Project Guide to Network Discovery and Security Scanning. Section 5.4: Stealth Scans (-sN, -sF, -sX). Saatavilla: https://nmap.org/book/man-port-scanning-techniques.html

Kali Linux Documentation: Port Scanning Techniques and Service Enumeration. Saatavilla: https://www.kali.org/tools/nmap/

RFC 793 (TCP Specification): Postel, J. (1981). Transmission Control Protocol - DARPA (Erityisesti osio 3.9 TCP-lippujen ja vastausten toiminnasta) Saatavilla: https://datatracker.ietf.org/doc/html/rfc793

e)

Nmap Project: Nmap Documentation: Command-line Flags & Options (-A, -T4, -p-). Saatavilla: https://nmap.org/book/man-briefoptions.html

NVD (National Vulnerability Database): CVE-2011-2523 - vsftpd 2.3.4 Backdoor execution. Saatavilla: https://nvd.nist.gov/vuln/detail/CVE-2011-2523

CVE Program: CVE-2010-2075 - UnrealIRCd 3.2.8.1 Backdoor Command Execution. Saatavilla: https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2010-2075

INCIBE-CERT / VulnDB: CVE-2004-2687 - distcc Arbitrary Command Execution. Saatavilla: https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2004-2687







  
