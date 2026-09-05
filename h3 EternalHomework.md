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
<img width="778" height="222" alt="image" src="https://github.com/user-attachments/assets/6b763889-406a-481d-8e1b-251fc7ae764a" />
<img width="412" height="34" alt="image" src="https://github.com/user-attachments/assets/8f9701c0-f86b-4a23-a17b-5d02cef213fd" />

### Komento `db_nmap -sV -A 192.168.56.10` metasplot framework:ssa:

<details>
  
<summary>Skannauksen tulos:</summary>

```text
msf > db_nmap -sV -A 192.168.56.102
[*] Nmap: Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-05 07:31 -0400
[*] Nmap: Nmap scan report for 192.168.56.102
[*] Nmap: Host is up (0.0027s latency).                                                                                                                                                                
[*] Nmap: Not shown: 977 closed tcp ports (reset)                                                                                                                                                      
[*] Nmap: PORT     STATE SERVICE     VERSION                                                                                                                                                           
[*] Nmap: 21/tcp   open  ftp         vsftpd 2.3.4                                                                                                                                                      
[*] Nmap: |_ftp-anon: Anonymous FTP login allowed (FTP code 230)                                                                                                                                       
[*] Nmap: | ftp-syst:                                                                                                                                                                                  
[*] Nmap: |   STAT:                                                                                                                                                                                    
[*] Nmap: | FTP server status:                                                                                                                                                                         
[*] Nmap: |      Connected to 192.168.56.101                                                                                                                                                           
[*] Nmap: |      Logged in as ftp                                                                                                                                                                      
[*] Nmap: |      TYPE: ASCII                                                                                                                                                                           
[*] Nmap: |      No session bandwidth limit                                                                                                                                                            
[*] Nmap: |      Session timeout in seconds is 300                                                                                                                                                     
[*] Nmap: |      Control connection is plain text                                                                                                                                                      
[*] Nmap: |      Data connections will be plain text                                                                                                                                                   
[*] Nmap: |      vsFTPd 2.3.4 - secure, fast, stable                                                                                                                                                   
[*] Nmap: |_End of status                                                                                                                                                                              
[*] Nmap: 22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)                                                                                                                      
[*] Nmap: | ssh-hostkey:                                                                                                                                                                               
[*] Nmap: |   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)                                                                                                                                                           
[*] Nmap: |_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)                                                                                                                                                           
[*] Nmap: 23/tcp   open  telnet      Linux telnetd                                                                                                                                                                                 
[*] Nmap: 25/tcp   open  smtp        Postfix smtpd
[*] Nmap: |_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
[*] Nmap: | ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
[*] Nmap: | Not valid before: 2010-03-17T14:07:45
[*] Nmap: |_Not valid after:  2010-04-16T14:07:45
[*] Nmap: | sslv2:
[*] Nmap: |   SSLv2 supported
[*] Nmap: |   ciphers:
[*] Nmap: |     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
[*] Nmap: |     SSL2_RC4_128_EXPORT40_WITH_MD5
[*] Nmap: |     SSL2_DES_64_CBC_WITH_MD5
[*] Nmap: |     SSL2_DES_192_EDE3_CBC_WITH_MD5
[*] Nmap: |     SSL2_RC2_128_CBC_WITH_MD5
[*] Nmap: |_    SSL2_RC4_128_WITH_MD5
[*] Nmap: |_ssl-date: 2026-09-05T11:29:14+00:00; -2m45s from scanner time.
[*] Nmap: 53/tcp   open  domain      ISC BIND 9.4.2
[*] Nmap: | dns-nsid:
[*] Nmap: |_  bind.version: 9.4.2
[*] Nmap: 80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
[*] Nmap: |_http-title: Metasploitable2 - Linux
[*] Nmap: |_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
[*] Nmap: 111/tcp  open  rpcbind     2 (RPC #100000)
[*] Nmap: | rpcinfo:
[*] Nmap: |   program version    port/proto  service
[*] Nmap: |   100000  2            111/tcp   rpcbind
[*] Nmap: |   100000  2            111/udp   rpcbind
[*] Nmap: |   100003  2,3,4       2049/tcp   nfs
[*] Nmap: |   100003  2,3,4       2049/udp   nfs
[*] Nmap: |   100005  1,2,3      35264/udp   mountd
[*] Nmap: |   100005  1,2,3      48310/tcp   mountd
[*] Nmap: |   100021  1,3,4      51222/udp   nlockmgr
[*] Nmap: |   100021  1,3,4      60696/tcp   nlockmgr
[*] Nmap: |   100024  1          41742/tcp   status
[*] Nmap: |_  100024  1          52091/udp   status
[*] Nmap: 139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: 445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
[*] Nmap: 512/tcp  open  exec        netkit-rsh rexecd
[*] Nmap: 513/tcp  open  login       OpenBSD or Solaris rlogind
[*] Nmap: 514/tcp  open  shell       Netkit rshd
[*] Nmap: 1099/tcp open  java-rmi    GNU Classpath grmiregistry
[*] Nmap: 1524/tcp open  bindshell   Metasploitable root shell
[*] Nmap: 2049/tcp open  nfs         2-4 (RPC #100003)
[*] Nmap: 2121/tcp open  ftp         ProFTPD 1.3.1
[*] Nmap: 3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
[*] Nmap: | mysql-info:
[*] Nmap: |   Protocol: 10
[*] Nmap: |   Version: 5.0.51a-3ubuntu5
[*] Nmap: |   Thread ID: 8
[*] Nmap: |   Capabilities flags: 43564
[*] Nmap: |   Some Capabilities: Speaks41ProtocolNew, Support41Auth, SupportsTransactions, SwitchToSSLAfterHandshake, SupportsCompression, LongColumnFlag, ConnectWithDatabase
[*] Nmap: |   Status: Autocommit
[*] Nmap: |_  Salt: y^J6mH[rLd5x~2y0|CvJ
[*] Nmap: 5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
[*] Nmap: |_ssl-date: 2026-09-05T11:29:14+00:00; -2m45s from scanner time.
[*] Nmap: | ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
[*] Nmap: | Not valid before: 2010-03-17T14:07:45
[*] Nmap: |_Not valid after:  2010-04-16T14:07:45
[*] Nmap: 5900/tcp open  vnc         VNC (protocol 3.3)
[*] Nmap: | vnc-info:
[*] Nmap: |   Protocol version: 3.3
[*] Nmap: |   Security types:
[*] Nmap: |_    VNC Authentication (2)
[*] Nmap: 6000/tcp open  X11         (access denied)
[*] Nmap: 6667/tcp open  irc         UnrealIRCd
[*] Nmap: 8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
[*] Nmap: |_ajp-methods: Failed to get a valid response for the OPTION request
[*] Nmap: 8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
[*] Nmap: |_http-server-header: Apache-Coyote/1.1
[*] Nmap: |_http-title: Apache Tomcat/5.5
[*] Nmap: |_http-favicon: Apache Tomcat
[*] Nmap: MAC Address: 08:00:27:67:E3:A5 (Oracle VirtualBox virtual NIC)
[*] Nmap: No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
[*] Nmap: TCP/IP fingerprint:
[*] Nmap: OS:SCAN(V=7.99%E=4%D=9/5%OT=21%CT=1%CU=37094%PV=Y%DS=1%DC=D%G=Y%M=080027%TM
[*] Nmap: OS:=6A9BFDAF%P=x86_64-pc-linux-gnu)SEQ(SP=C7%GCD=1%ISR=D5%TI=Z%CI=Z%II=I%TS
[*] Nmap: OS:=6)SEQ(SP=C8%GCD=1%ISR=C9%TI=Z%CI=Z%II=I%TS=6)SEQ(SP=CB%GCD=1%ISR=CE%TI=
[*] Nmap: OS:Z%CI=Z%II=I%TS=6)SEQ(SP=CC%GCD=1%ISR=CF%TI=Z%CI=Z%II=I%TS=6)SEQ(SP=CD%GC
[*] Nmap: OS:D=1%ISR=CE%TI=Z%CI=Z%II=I%TS=6)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW7%O3=M5B4
[*] Nmap: OS:NNT11NW7%O4=M5B4ST11NW7%O5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=16A0%W2=16A0%W
[*] Nmap: OS:3=16A0%W4=16A0%W5=16A0%W6=16A0)ECN(R=Y%DF=Y%T=40%W=16D0%O=M5B4NNSNW7%CC=
[*] Nmap: OS:N%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=Y%DF=Y%T=40%W=16
[*] Nmap: OS:A0%S=O%A=S+%F=AS%O=M5B4ST11NW7%RD=0%Q=)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%
[*] Nmap: OS:O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=4
[*] Nmap: OS:0%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%
[*] Nmap: OS:Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=
[*] Nmap: OS:Y%DFI=N%T=40%CD=S)
[*] Nmap: Network Distance: 1 hop
[*] Nmap: Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
[*] Nmap: Host script results:
[*] Nmap: |_smb2-time: Protocol negotiation failed (SMB2)
[*] Nmap: | smb-os-discovery:
[*] Nmap: |   OS: Unix (Samba 3.0.20-Debian)
[*] Nmap: |   Computer name: metasploitable
[*] Nmap: |   NetBIOS computer name:
[*] Nmap: |   Domain name: localdomain
[*] Nmap: |   FQDN: metasploitable.localdomain
[*] Nmap: |_  System time: 2026-09-05T07:29:10-04:00
[*] Nmap: |_nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
[*] Nmap: |_clock-skew: mean: 57m17s, deviation: 2h00m02s, median: -2m45s
[*] Nmap: | smb-security-mode:
[*] Nmap: |   account_used: <blank>
[*] Nmap: |   authentication_level: user
[*] Nmap: |   challenge_response: supported
[*] Nmap: |_  message_signing: disabled (dangerous, but default)
[*] Nmap: TRACEROUTE
[*] Nmap: HOP RTT     ADDRESS
[*] Nmap: 1   2.74 ms 192.168.56.102
[*] Nmap: OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 33.93 seconds
/usr/share/metasploit-framework/vendor/bundle/ruby/3.3.0/gems/recog-3.1.29/lib/recog/fingerprint/regexp_factory.rb:34: warning: nested repeat operator '+' and '?' was replaced with '*' in regular expression

 ```
</details>

## b)

<img width="785" height="143" alt="image" src="https://github.com/user-attachments/assets/395337c9-dcd8-4581-a159-3546c136c703" />


<img width="790" height="128" alt="image" src="https://github.com/user-attachments/assets/a94b0cef-b7b3-40a0-a249-47ca2f36e50f" />


<img width="942" height="471" alt="image" src="https://github.com/user-attachments/assets/bf6ad132-426b-4af8-b5bd-17e8f9dede65" />


<img width="609" height="121" alt="image" src="https://github.com/user-attachments/assets/5080773f-f862-4297-9550-98c7e3862a97" />



## c)

Metasploitable 2 sisältää UnrealIRCD 3.2.8.1 Backdoor -haavoittuvuuden (CVE-2010-2075). Tämä takaovi päätyi viralliseen latauspakettiin vuonna 2009-2010, kun hyökkääjä murtautui UnrealIRCd:n peilipalvelimelle ja korvasi lähdekooditiedoston haitallisella versiolla. 
Tapaus sai laajaa huomiota tietoturvamedioissa, sillä se osoitti ohjelmistotuotantoketjun (Supply Chain Attack) haavoittuvuuden.

Haun suorittaminen Metasploitissa (`msf6 > search unrealircd`):

<img width="1057" height="195" alt="image" src="https://github.com/user-attachments/assets/f7957bed-b5b8-458d-ba65-db618a3f3c75" />



## d) Nmap tiedoston tallennus vs. Metasploit tallennus

### Nmap

Kun suorittaa komennon `nmap -oA scan_results 192.168.56.102` nmap tallentaa skannin tulokset kolmeen tiedostoon:
* `scan_tulokset.nmap`
  
<details>
  
  <sunmmary>Tulokset:</sunmmary>
  
  ```text
┌──(kali㉿kali)-[~/nmap.results]
└─$ cat scan_results.nmap 
# Nmap 7.99 scan initiated Sat Sep  5 07:53:08 2026 as: /usr/lib/nmap/nmap --privileged -oA scan_results 192.168.56.102
Nmap scan report for 192.168.56.102
Host is up (0.0079s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 08:00:27:67:E3:A5 (Oracle VirtualBox virtual NIC)

# Nmap done at Sat Sep  5 07:53:09 2026 -- 1 IP address (1 host up) scanned in 0.76 seconds


   ```

</details>
  
* `scan_tulokset.gnmap`

<details>
  <sunmmary>Tulokset:</sunmmary>
  
```text
┌──(kali㉿kali)-[~/nmap.results]
└─$ cat scan_results.gnmap 
# Nmap 7.99 scan initiated Sat Sep  5 07:53:08 2026 as: /usr/lib/nmap/nmap --privileged -oA scan_results 192.168.56.102
Host: 192.168.56.102 () Status: Up
Host: 192.168.56.102 () Ports: 21/open/tcp//ftp///, 22/open/tcp//ssh///, 23/open/tcp//telnet///, 25/open/tcp//smtp///, 53/open/tcp//domain///, 80/open/tcp//http///, 111/open/tcp//rpcbind///, 139/open/tcp//netbios-ssn///, 445/open/tcp//microsoft-ds///, 512/open/tcp//exec///, 513/open/tcp//login///, 514/open/tcp//shell///, 1099/open/tcp//rmiregistry///, 1524/open/tcp//ingreslock///, 2049/open/tcp//nfs///, 2121/open/tcp//ccproxy-ftp///, 3306/open/tcp//mysql///, 5432/open/tcp//postgresql///, 5900/open/tcp//vnc///, 6000/open/tcp//X11///, 6667/open/tcp//irc///, 8009/open/tcp//ajp13///, 8180/open/tcp///// Ignored State: closed (977)
# Nmap done at Sat Sep  5 07:53:09 2026 -- 1 IP address (1 host up) scanned in 0.76 seconds



   ```

</details>
  
* `scan_tulokset.xml`
  
<details>
  <sunmmary>Tulokset:</sunmmary>
  
  ```text
┌──(kali㉿kali)-[~/nmap.results]
└─$ cat scan_results.xml 
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE nmaprun>
<?xml-stylesheet href="file:///usr/share/nmap/nmap.xsl" type="text/xsl"?>
<!-- Nmap 7.99 scan initiated Sat Sep  5 07:53:08 2026 as: /usr/lib/nmap/nmap -&#45;privileged -oA scan_results 192.168.56.102 -->
<nmaprun scanner="nmap" args="/usr/lib/nmap/nmap -&#45;privileged -oA scan_results 192.168.56.102" start="1788609188" startstr="Sat Sep  5 07:53:08 2026" version="7.99" xmloutputversion="1.05">
<scaninfo type="syn" protocol="tcp" numservices="1000" services="1,3-4,6-7,9,13,17,19-26,30,32-33,37,42-43,49,53,70,79-85,88-90,99-100,106,109-111,113,119,125,135,139,143-144,146,161,163,179,199,211-212,222,254-256,259,264,280,301,306,311,340,366,389,406-407,416-417,425,427,443-445,458,464-465,481,497,500,512-515,524,541,543-545,548,554-555,563,587,593,616-617,625,631,636,646,648,666-668,683,687,691,700,705,711,714,720,722,726,749,765,777,783,787,800-801,808,843,873,880,888,898,900-903,911-912,981,987,990,992-993,995,999-1002,1007,1009-1011,1021-1100,1102,1104-1108,1110-1114,1117,1119,1121-1124,1126,1130-1132,1137-1138,1141,1145,1147-1149,1151-1152,1154,1163-1166,1169,1174-1175,1183,1185-1187,1192,1198-1199,1201,1213,1216-1218,1233-1234,1236,1244,1247-1248,1259,1271-1272,1277,1287,1296,1300-1301,1309-1311,1322,1328,1334,1352,1417,1433-1434,1443,1455,1461,1494,1500-1501,1503,1521,1524,1533,1556,1580,1583,1594,1600,1641,1658,1666,1687-1688,1700,1717-1721,1723,1755,1761,1782-1783,1801,1805,1812,1839-1840,1862-1864,1875,1900,1914,1935,1947,1971-1972,1974,1984,1998-2010,2013,2020-2022,2030,2033-2035,2038,2040-2043,2045-2049,2065,2068,2099-2100,2103,2105-2107,2111,2119,2121,2126,2135,2144,2160-2161,2170,2179,2190-2191,2196,2200,2222,2251,2260,2288,2301,2323,2366,2381-2383,2393-2394,2399,2401,2492,2500,2522,2525,2557,2601-2602,2604-2605,2607-2608,2638,2701-2702,2710,2717-2718,2725,2800,2809,2811,2869,2875,2909-2910,2920,2967-2968,2998,3000-3001,3003,3005-3006,3011,3017,3030-3031,3052,3071,3077,3128,3168,3211,3221,3260-3261,3268-3269,3283,3300-3301,3306,3322-3325,3333,3351,3367,3369-3372,3389-3390,3404,3476,3493,3517,3527,3546,3551,3580,3659,3689-3690,3703,3737,3766,3784,3800-3801,3809,3814,3826-3828,3851,3869,3871,3878,3880,3889,3905,3914,3918,3920,3945,3971,3986,3995,3998,4000-4006,4045,4111,4125-4126,4129,4224,4242,4279,4321,4343,4443-4446,4449,4550,4567,4662,4848,4899-4900,4998,5000-5004,5009,5030,5033,5050-5051,5054,5060-5061,5080,5087,5100-5102,5120,5190,5200,5214,5221-5222,5225-5226,5269,5280,5298,5357,5405,5414,5431-5432,5440,5500,5510,5544,5550,5555,5560,5566,5631,5633,5666,5678-5679,5718,5730,5800-5802,5810-5811,5815,5822,5825,5850,5859,5862,5877,5900-5904,5906-5907,5910-5911,5915,5922,5925,5950,5952,5959-5963,5985-5989,5998-6007,6009,6025,6059,6100-6101,6106,6112,6123,6129,6156,6346,6389,6502,6510,6543,6547,6565-6567,6580,6646,6666-6669,6689,6692,6699,6779,6788-6789,6792,6839,6881,6901,6969,7000-7002,7004,7007,7019,7025,7070,7100,7103,7106,7200-7201,7402,7435,7443,7496,7512,7625,7627,7676,7741,7777-7778,7800,7911,7920-7921,7937-7938,7999-8002,8007-8011,8021-8022,8031,8042,8045,8080-8090,8093,8099-8100,8180-8181,8192-8194,8200,8222,8254,8290-8292,8300,8333,8383,8400,8402,8443,8500,8600,8649,8651-8652,8654,8701,8800,8873,8888,8899,8994,9000-9003,9009-9011,9040,9050,9071,9080-9081,9090-9091,9099-9103,9110-9111,9200,9207,9220,9290,9415,9418,9485,9500,9502-9503,9535,9575,9593-9595,9618,9666,9876-9878,9898,9900,9917,9929,9943-9944,9968,9998-10004,10009-10010,10012,10024-10025,10082,10180,10215,10243,10566,10616-10617,10621,10626,10628-10629,10778,11110-11111,11967,12000,12174,12265,12345,13456,13722,13782-13783,14000,14238,14441-14442,15000,15002-15004,15660,15742,16000-16001,16012,16016,16018,16080,16113,16992-16993,17877,17988,18040,18101,18988,19101,19283,19315,19350,19780,19801,19842,20000,20005,20031,20221-20222,20828,21571,22939,23502,24444,24800,25734-25735,26214,27000,27352-27353,27355-27356,27715,28201,30000,30718,30951,31038,31337,32768-32785,33354,33899,34571-34573,35500,38292,40193,40911,41511,42510,44176,44442-44443,44501,45100,48080,49152-49161,49163,49165,49167,49175-49176,49400,49999-50003,50006,50300,50389,50500,50636,50800,51103,51493,52673,52822,52848,52869,54045,54328,55055-55056,55555,55600,56737-56738,57294,57797,58080,60020,60443,61532,61900,62078,63331,64623,64680,65000,65129,65389"/>
<verbose level="0"/>
<debugging level="0"/>
<hosthint><status state="up" reason="arp-response" reason_ttl="0"/>
<address addr="192.168.56.102" addrtype="ipv4"/>
<address addr="08:00:27:67:E3:A5" addrtype="mac" vendor="Oracle VirtualBox virtual NIC"/>
<hostnames>
</hostnames>
</hosthint>
<host starttime="1788609188" endtime="1788609189"><status state="up" reason="arp-response" reason_ttl="0"/>
<address addr="192.168.56.102" addrtype="ipv4"/>
<address addr="08:00:27:67:E3:A5" addrtype="mac" vendor="Oracle VirtualBox virtual NIC"/>
<hostnames>
</hostnames>
<ports><extraports state="closed" count="977">
<extrareasons reason="reset" count="977" proto="tcp" ports="1,3-4,6-7,9,13,17,19-20,24,26,30,32-33,37,42-43,49,70,79,81-85,88-90,99-100,106,109-110,113,119,125,135,143-144,146,161,163,179,199,211-212,222,254-256,259,264,280,301,306,311,340,366,389,406-407,416-417,425,427,443-444,458,464-465,481,497,500,515,524,541,543-545,548,554-555,563,587,593,616-617,625,631,636,646,648,666-668,683,687,691,700,705,711,714,720,722,726,749,765,777,783,787,800-801,808,843,873,880,888,898,900-903,911-912,981,987,990,992-993,995,999-1002,1007,1009-1011,1021-1098,1100,1102,1104-1108,1110-1114,1117,1119,1121-1124,1126,1130-1132,1137-1138,1141,1145,1147-1149,1151-1152,1154,1163-1166,1169,1174-1175,1183,1185-1187,1192,1198-1199,1201,1213,1216-1218,1233-1234,1236,1244,1247-1248,1259,1271-1272,1277,1287,1296,1300-1301,1309-1311,1322,1328,1334,1352,1417,1433-1434,1443,1455,1461,1494,1500-1501,1503,1521,1533,1556,1580,1583,1594,1600,1641,1658,1666,1687-1688,1700,1717-1721,1723,1755,1761,1782-1783,1801,1805,1812,1839-1840,1862-1864,1875,1900,1914,1935,1947,1971-1972,1974,1984,1998-2010,2013,2020-2022,2030,2033-2035,2038,2040-2043,2045-2048,2065,2068,2099-2100,2103,2105-2107,2111,2119,2126,2135,2144,2160-2161,2170,2179,2190-2191,2196,2200,2222,2251,2260,2288,2301,2323,2366,2381-2383,2393-2394,2399,2401,2492,2500,2522,2525,2557,2601-2602,2604-2605,2607-2608,2638,2701-2702,2710,2717-2718,2725,2800,2809,2811,2869,2875,2909-2910,2920,2967-2968,2998,3000-3001,3003,3005-3006,3011,3017,3030-3031,3052,3071,3077,3128,3168,3211,3221,3260-3261,3268-3269,3283,3300-3301,3322-3325,3333,3351,3367,3369-3372,3389-3390,3404,3476,3493,3517,3527,3546,3551,3580,3659,3689-3690,3703,3737,3766,3784,3800-3801,3809,3814,3826-3828,3851,3869,3871,3878,3880,3889,3905,3914,3918,3920,3945,3971,3986,3995,3998,4000-4006,4045,4111,4125-4126,4129,4224,4242,4279,4321,4343,4443-4446,4449,4550,4567,4662,4848,4899-4900,4998,5000-5004,5009,5030,5033,5050-5051,5054,5060-5061,5080,5087,5100-5102,5120,5190,5200,5214,5221-5222,5225-5226,5269,5280,5298,5357,5405,5414,5431,5440,5500,5510,5544,5550,5555,5560,5566,5631,5633,5666,5678-5679,5718,5730,5800-5802,5810-5811,5815,5822,5825,5850,5859,5862,5877,5901-5904,5906-5907,5910-5911,5915,5922,5925,5950,5952,5959-5963,5985-5989,5998-5999,6001-6007,6009,6025,6059,6100-6101,6106,6112,6123,6129,6156,6346,6389,6502,6510,6543,6547,6565-6567,6580,6646,6666,6668-6669,6689,6692,6699,6779,6788-6789,6792,6839,6881,6901,6969,7000-7002,7004,7007,7019,7025,7070,7100,7103,7106,7200-7201,7402,7435,7443,7496,7512,7625,7627,7676,7741,7777-7778,7800,7911,7920-7921,7937-7938,7999-8002,8007-8008,8010-8011,8021-8022,8031,8042,8045,8080-8090,8093,8099-8100,8181,8192-8194,8200,8222,8254,8290-8292,8300,8333,8383,8400,8402,8443,8500,8600,8649,8651-8652,8654,8701,8800,8873,8888,8899,8994,9000-9003,9009-9011,9040,9050,9071,9080-9081,9090-9091,9099-9103,9110-9111,9200,9207,9220,9290,9415,9418,9485,9500,9502-9503,9535,9575,9593-9595,9618,9666,9876-9878,9898,9900,9917,9929,9943-9944,9968,9998-10004,10009-10010,10012,10024-10025,10082,10180,10215,10243,10566,10616-10617,10621,10626,10628-10629,10778,11110-11111,11967,12000,12174,12265,12345,13456,13722,13782-13783,14000,14238,14441-14442,15000,15002-15004,15660,15742,16000-16001,16012,16016,16018,16080,16113,16992-16993,17877,17988,18040,18101,18988,19101,19283,19315,19350,19780,19801,19842,20000,20005,20031,20221-20222,20828,21571,22939,23502,24444,24800,25734-25735,26214,27000,27352-27353,27355-27356,27715,28201,30000,30718,30951,31038,31337,32768-32785,33354,33899,34571-34573,35500,38292,40193,40911,41511,42510,44176,44442-44443,44501,45100,48080,49152-49161,49163,49165,49167,49175-49176,49400,49999-50003,50006,50300,50389,50500,50636,50800,51103,51493,52673,52822,52848,52869,54045,54328,55055-55056,55555,55600,56737-56738,57294,57797,58080,60020,60443,61532,61900,62078,63331,64623,64680,65000,65129,65389"/>
</extraports>
<port protocol="tcp" portid="21"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="ftp" method="table" conf="3"/></port>
<port protocol="tcp" portid="22"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="ssh" method="table" conf="3"/></port>
<port protocol="tcp" portid="23"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="telnet" method="table" conf="3"/></port>
<port protocol="tcp" portid="25"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="smtp" method="table" conf="3"/></port>
<port protocol="tcp" portid="53"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="domain" method="table" conf="3"/></port>
<port protocol="tcp" portid="80"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="http" method="table" conf="3"/></port>
<port protocol="tcp" portid="111"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="rpcbind" method="table" conf="3"/></port>
<port protocol="tcp" portid="139"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="netbios-ssn" method="table" conf="3"/></port>
<port protocol="tcp" portid="445"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="microsoft-ds" method="table" conf="3"/></port>
<port protocol="tcp" portid="512"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="exec" method="table" conf="3"/></port>
<port protocol="tcp" portid="513"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="login" method="table" conf="3"/></port>
<port protocol="tcp" portid="514"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="shell" method="table" conf="3"/></port>
<port protocol="tcp" portid="1099"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="rmiregistry" method="table" conf="3"/></port>
<port protocol="tcp" portid="1524"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="ingreslock" method="table" conf="3"/></port>
<port protocol="tcp" portid="2049"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="nfs" method="table" conf="3"/></port>
<port protocol="tcp" portid="2121"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="ccproxy-ftp" method="table" conf="3"/></port>
<port protocol="tcp" portid="3306"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="mysql" method="table" conf="3"/></port>
<port protocol="tcp" portid="5432"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="postgresql" method="table" conf="3"/></port>
<port protocol="tcp" portid="5900"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="vnc" method="table" conf="3"/></port>
<port protocol="tcp" portid="6000"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="X11" method="table" conf="3"/></port>
<port protocol="tcp" portid="6667"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="irc" method="table" conf="3"/></port>
<port protocol="tcp" portid="8009"><state state="open" reason="syn-ack" reason_ttl="64"/><service name="ajp13" method="table" conf="3"/></port>
<port protocol="tcp" portid="8180"><state state="open" reason="syn-ack" reason_ttl="64"/></port>
</ports>
<times srtt="7904" rttvar="79" to="100000"/>
</host>
<runstats><finished time="1788609189" timestr="Sat Sep  5 07:53:09 2026" summary="Nmap done at Sat Sep  5 07:53:09 2026; 1 IP address (1 host up) scanned in 0.76 seconds" elapsed="0.76" exit="success"/><hosts up="1" down="0" total="1"/>
</runstats>
</nmaprun>

   ```

</details>



### Metasploit

Kun suorittaa komennon `db_nmap -sV 192.168.56.102` MSFconsolessa, tiedot eivät tallennu tavalliseksi tiedostoksi, vaan Kalin taustalla pyörivään PostgreSQL-tietokantaan.
Tiedot haetaan suoraan `msfconsole:n` sisältä komennoilla:
* `hosts`
* `services`
  
<img width="956" height="622" alt="image" src="https://github.com/user-attachments/assets/8f44974e-c7ed-49f5-8515-e1e05180d3ec" />

Tietoja pystyy siirtää (export) tietokannasta suoraan tiedostoon komennolla:
`db_export xml /home/kali/msf_database_export.xml`


### Ominaisuuksien vertailu ja yhteenveto

| Ominaisuus | Nmap-tiedostot (`-oA`) | Metasploit DB (`db_nmap`) |
| :--- | :--- | :--- |
| Tallennuspaikka | Paikallinen kansio (`.nmap`, `.gnmap`, `.xml`) | PostgreSQL-tietokanta |
| Käytettävyys | Helppo käsitellä peruskomennolla (`grep`, `cat`) | Kyselyt tehdään msfconsolessa (`hosts`, `services`) |
| Jatkokäyttö | Pitää kopioida/syöttää käsin muihin työkaluihin | Tulokset saa suoraan hyökkäysmoduulin kohteeksi (`services -R`) |
| Käyttökohde | Yksittäiset skannaukset ja raportointi | Laajemmat tunkeutumistestit ja target-hallinta |


Yhteenveto:`-oA`-tiedostomuodot sopivat parhaiten dokumentointiin, raportointiin ja nopeutta vaativaan CLI-suodatukseen (`grep`). Metasploitin tietokanta taas on paras työkalu hyökkäysvaiheessa, kun skannaustulokset halutaan syöttää suoraan hyökkäysmoduulien kohteeksi ilman IP-osoitteiden käsin syöttämistä.


Lähde: 

*Nmap Output Options (`-oA`)*:
https://nmap.org/book/man-output.html 


*Using the Metasploit Database & db_nmap*:
https://docs.metasploit.com/docs/using-metasploit/intermediate/metasploit-database-support.html


## d) Metasploitablen vsftpd-palveluun murtautuminen

<img width="966" height="202" alt="image" src="https://github.com/user-attachments/assets/fff89a1b-eead-4038-a0b8-6ee9be4d3966" />

Tiedetään, että kohdekoneessa (metasploitable `192.168.52.102`) pyörii vsftpd palvelu versio 2.3.4 portissa 21. Tämä versio tunnetaan siitä, että siihen päätyi vuonna 2011 haitallinen takaovi, joka avaa pääkäyttäjän (root) komentotulkin porttiin 6200, kun käyttäjätunnuksen perään syötetään hymiö :).




<img width="1662" height="242" alt="image" src="https://github.com/user-attachments/assets/535c2f8f-43cb-43c7-9e6b-eb6f878256e2" />

### Hyökkäyksen suoritus Metasploitilla
Valitsin hyökkäysmoduuli `exploit/unix/ftp/vsftpd_234_backdoor` ja määritettiin hyökkäysmoduulin tarvittavat `RHOSTS` ja `LHOST` muuttujat:
* `RHOSTS` (`192.168.56.102`) Kohdekoneen Metasploitable2 IP-osoite
* `LHOST` (`192.168.56.101`) Kali hyökkäyskoneen IP-osoite, osoite tarvitaan paluuyhteyttä varten.
  
Hyökkäyksen suoritus laukaisi takaoven kohteessa ja avasi Meterpreter-session (`Meterpreter session 1 opened`)


### Saatujen tietojen analysointi
Meterpreter-sessiossa ajoin komennot `sysinfo` ja `getuid`.

<img width="439" height="144" alt="image" src="https://github.com/user-attachments/assets/34aa5fc9-88f6-4610-87b4-249613c8570c" />

Merkitys ja analyysi:
* Käyttäjäoikeudet (`Server username: root`): Hyökkäys tuotti välittömästi root oikeudet tämä on kätevä, koska ei pidä erikseen suorittaa komentoa jolla hyökkääjä korottaa oikeuksiaan root tasolle vaan järjestelmä on täysin hallittavissa jo tässä vaiheessa.
* Käyttöjärjestelmä (`Ubuntu 8.04 / Linux 2.6.24-16-server`): Kohteella on käytössä vanha Ubuntu versio julkaistu 2008 luvulla, tämä selittää miksi koneella on suuri hyökkäyspinta.
* Koska hyökkääjällä on `root` oikeudet, järjestelmästä voidaan esimerkiksi kerätä kaikkien käyttäjien tiedot, asentaa pysyviä takaovia tai liikkua toisiin verkossa oleviin laitteisiin.

Lähde: 

CVE-2011-2523 Detail (vsFTPd v2.3.4 Backdoor): https://nvd.nist.gov/vuln/detail/cve-2011-2523

VSFTPD v2.3.4 Backdoor Command Execution: https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor


## e) Lateral movement Metasploit:ssa ja tieotojen analysointi

Kohde laitteen/verkon tiedon keryy tavoitteena on selvittää verkkoympäristö, aktiiviset yhteydet, tallennetut tunnistetiedot sekä muut järjestelmässä vierailevat käyttäjät.

### Verkkoympäristön ja aliverkkojen kartoitus (`ifconfig` ja `arp`)
<img width="392" height="385" alt="image" src="https://github.com/user-attachments/assets/a55541aa-e3e9-4838-890a-862e3540e233" />

<img width="414" height="159" alt="image" src="https://github.com/user-attachments/assets/e5993f18-7ede-45ee-bcf4-ad23d3479e89" />

* Kohdekoneen verkkokorttien IP-osoitteet, aliverkon peitteet sekä ARP-taulukko, johon on tallentunut muiden samassa verkkosegmentissä viestineiden laitteiden MAC- ja IP-osoitteet.
* ARP-taulukko paljastaa aktiiviset naapurikoneet ilman, että verkkoon tarvitsee ajaa mahdollisesti äänekästä nmap skannausta.

### Aktiiviset verkkoyhteydet (`netstat`)

<img width="796" height="1032" alt="netstat" src="https://github.com/user-attachments/assets/785d57e0-d3d5-4092-8d7f-4ab6b43ac039" />

* Kaikki kohdekoneen avaamat ja kuuntelemat TCP/UDP-yhteydet, kuuntelevat portit sekä vastapuolen IP-osoitteet.


### Tunnistetietojen kerääminen (`/etc/shadow`)

<img width="559" height="589" alt="hashdump" src="https://github.com/user-attachments/assets/421fd5da-ee1b-43a8-9e8e-a1152ed93955" />

 
* Komennon `cat /etc/shadow` suorittaminen tuottaa kaikkien jätjestelmän käyttäjien salasanat `hash` muodossa.
  * `shadow` tiedosto onm paikka johon tallennetaan käyttäjien salasana hashit, tiedoston lukuoikeus on pelkästään `root` käyttäjällä.
* Salasanamurto hashit voidaan kopioida Kali koneelle ja murtaa esimerkiksi Hashcat tai John the Ripper työkaluilla.
* Tunnusten uudelleenkäyttö ihmiset usein käyttävät samoja salasanoja eri paikoissa salasanojen uudelleenkäyttö voi avata ovia.

Lähteet:
MITRE ATT&CK Framework: Tactic TA0008 - Lateral Movement: https://attack.mitre.org/tactics/TA0008/

## f) Metasploitabe2 koneelle tunkeutuminen tosisella tavalla
Hyökkään metasploitable2 koneelle Samba-palvelun (portti 139/445) "usermap script" -haavoittuvuuden (CVE-2007-2447) avulla.


### Kohteen skannaus Nmapilla

<img width="991" height="656" alt="image" src="https://github.com/user-attachments/assets/7f13f04a-bd82-4476-b4ea-bdefe66a8c3a" />


Nmap skannista näkyy, että Samba-tiedostojonopalvelu pyörii porteilla 139 ja 445. Kohdekoneella Samba pyörii versiolla 3.0.20, tämä on tärkeää tietoa jonka avula voidaan löytää Metasploitissa sopiva hyökkäysmoduuli. 


### Haavoittuvuuden haku Metasploitissa 
Avaan MSFconsole:n ja etsin tunnistetulle Samba 3.0.20 versiolle sopivaa hyökkäysmoduulia:

<img width="1001" height="191" alt="image" src="https://github.com/user-attachments/assets/3047a700-7319-4412-89db-f06621def55b" />

Hakutuloksiin ilmestyy moduuli `exploit/multi/samba/usermap_script`, joka mahdollistaa koodin suorituksen (`Command Execution`) Samban syötteenkäsiteltävyysvirheen kautta.

### Hyökkäyksen suoritus
Valitsen moduulin käyttöön ja asetetaan kohteeksi Metasploitable 2 sekä hyökkääjäksi Kali Linux:

<img width="933" height="243" alt="image" src="https://github.com/user-attachments/assets/2c07c94c-af0a-411e-99b8-d1edbac540c9" />

* `whoami` ja `id` komentojem vastaukseiksi saadaan `root` ja `uid=0(root)`, tämä vahvistaa murtautumisen onnistuneeksi!


### Yhteenveto

Hyökkäyksen suorittaminen laukaisi käänteisen shell-yhteyden (`cmd/unix/reverse_netcat`) kohdekoneelta takaisin Kali Linux -hyökkäyskoneelle. Yhteyden avauduttua saavutettu käyttöoikeustaso varmistettiin ajamalla järjestelmäkomennot `whoami` ja `id`:
* Käänteinen shell-yhteys (`reverse shell`): Hyökkäystapa, jossa kohdekone pakotetaan ottamaan itse yhteys ulospäin hyökkääjän koneelle.
* Samba-palvelu pyörii kohdejärjestelmässä taustalla `root` oikeuksin. Koska syötteenpuhdistuksen puute (ohjelmointivirhe 3.0.20 versiossa) mahdollisti komento-injektion suoraan Samban prosessiin, hyökkääjä saavutti välittömästi järjestelmän korkeimman hallintatason ilman tarvetta erilliselle oikeuksien korottamiselle

Lähteet:

NIST National Vulnerability Database: CVE-2007-2447 Detail: https://nvd.nist.gov/vuln/detail/CVE-2007-2447

apid7 Vulnerability Database: Samba "username map script" Command Execution: https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script/

Käytin jonkin verran apuani CYB3RLEO:n raporttia: https://github.com/CYB3RLEO/Penenetration_Testing_Lab_Exploitation_Phase3-Metasploitable3-samba_user_map-


## g) Meterpretrin ominaisuudet

### Järjestelmä -ja prosessitiedot (`sysinfo` ja `ps`)
<img width="438" height="127" alt="image" src="https://github.com/user-attachments/assets/3a22ad4c-5beb-4ab3-b41f-1764b910e939" />

* Komento `sysinfo` antaa välittömän yhteenvedon käyttöjärjestelmästä ja arkkitehtuurista.

<img width="564" height="784" alt="image" src="https://github.com/user-attachments/assets/7d11295a-2543-4f4e-b536-37c8b02019da" />


* `ps` komento listaa kaikki käynnissä olevat prosessit, niiden prosessi-ID:t (PID) sekä niitä ajavat käyttäjät.



### Verkkoympäristön kartoitus (`ifconfig`)

<img width="406" height="391" alt="image" src="https://github.com/user-attachments/assets/2bf55cd8-8809-47d2-90d0-fa5349142d1c" />

* Näyttää kohdekoneen kaikkien verkkokorttien tiedot.

### Shell

<img width="217" height="71" alt="image" src="https://github.com/user-attachments/assets/c023a8f8-c267-41ff-a3d9-ce3237269068" />

* Jos Meterpreterin omat komennot eivät riitä, `shell` komento avaa suoran yhteyskanavan kohdejärjestelmän omaan Linux-komentotulkkiin, jonka avulla voidaan ajaa mitä tahansa komentoja tai skriptejä.

Lähteet:

About the Metasploit Meterpreter - Offsec: https://www.offsec.com/metasploit-unleashed/about-meterpreter/

Metasploit Documentation: `man` sivut

## h) shell-session tallennus script työkalulla

### Lokituksen käynnistäminen

<img width="394" height="62" alt="image" src="https://github.com/user-attachments/assets/60cf4ba6-cc1d-4edd-b701-7c87af82f81b" />


* Liput: `-f` kirjoittaa komennot tiedostoon reaaliajassa ja `-a` lisää tiedot tiedoston perään.


### Hyökkäys Metasploit:lla 
Käynnistin Metasploitin `msfconsole` komennolla ja käytin aikasemminkin käyttämäni vsftpd haavoittuvuutta:



<img width="1662" height="242" alt="image" src="https://github.com/user-attachments/assets/7f500e5f-e450-4502-90dc-d8df30d63b6a" />


<img width="439" height="144" alt="image" src="https://github.com/user-attachments/assets/454aa5ab-06a1-43c1-ab57-674caf58866b" />


### Lokien tarkistaminen
Lokien sisältö (cat logo001.txt):

<img width="723" height="690" alt="image" src="https://github.com/user-attachments/assets/cbf2ecee-aabc-478a-a1d9-fc4ee742aea4" />

* Tekstitiedostosta löytyy msfconsole:ssa suorittamani komennot ja ajat millon suoritin ne.

Lähteet:
Komentorivin lokitus: Linux Programmer's Manual:
https://man7.org/linux/man-pages/man1/script.1.html

Meterpreter-komennot: OffSec Metasploit Unleashed Meterpreter Basics:

https://www.offsec.com/metasploit-unleashed/meterpreter-basics

## j) Pivot point














