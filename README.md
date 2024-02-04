# AV-alused

## Networking for Web Developers

### From Ping to HTTP

#### Setting Up For This Course
Seadistan masina Local VM-na, selleks installin VirtualBox ning Vagranti ning jooksutan õpetuses välja toodud commande. Juurde pääsen sellele kui lähen kausta, kuhu selle seadistasin ning runin commandi `vagrant ssh`. Esimesel korral küsis ta minult passwordi 3 korda ning iga kord sisestasin `vagrant`. Peale seda sain ligipääsu ja seal runisin `sudo apt-get update && sudo apt-get upgrade` ning `sudo apt-get install netcat-openbsd tcpdump traceroute mtr`. Iga kord kui taas avan gitbashi, et kasutada vagranti runin `cd networking`, `vagrant up` ning siis `vagrant ssh` ning password vagrant.

#### Try some network things!
Edasi runisin kõik videos mainitud commandid:
`ip addr show eth0` - 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:5f:bb:e6 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe5f:bbe6/64 scope link
       valid_lft forever preferred_lft forever
       
`ip route show` - default via 10.0.2.2 dev eth0
10.0.2.0/24 dev eth0  proto kernel  scope link  src 10.0.2.15

`ping -c3 8.8.8.8` - PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=55 time=8.88 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=55 time=8.15 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=55 time=6.55 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 6.556/7.866/8.885/0.975 ms

`host -t aaaa google.com` - google.com has IPv6 address 2a00:1450:4026:808::200e

`host -t mx udacity.com` - udacity.com mail is handled by 30 alt3.aspmx.l.google.com.
udacity.com mail is handled by 30 alt4.aspmx.l.google.com.
udacity.com mail is handled by 20 alt1.aspmx.l.google.com.
udacity.com mail is handled by 20 alt2.aspmx.l.google.com.
udacity.com mail is handled by 10 aspmx.l.google.com.

`tcpdump -n -c5 -i eth0 port 22` - tcpdump: eth0: You don't have permission to capture on that device
(socket: Operation not permitted)

`traceroute www.udacity.com` - traceroute to www.udacity.com (104.18.5.237), 30 hops max, 60 byte packets
 1  10.0.2.2 (10.0.2.2)  0.129 ms  0.115 ms  0.108 ms
 2  * * *
 3  * * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *

`mtr  www.udacity.com` - sain tabeli mis jooksis kuni peatasin selle Q-ga
vagrant-ubuntu-trusty-64 (0.0.0.0)                                                             Sun Jan  7 12:54:49 2024
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                                               Packets               Pings
 Host                                                                        Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 10.0.2.2                                                                  0.0%    50    1.1   0.6   0.2   1.2   0.0
 2. dsldevice.lan                                                             0.0%    50    2.4   1.8   0.9   4.1   0.3
 3. ???
 4. talvil-m2k1-lag7.elisa.ee                                                 0.0%    50    4.4   7.4   2.6  18.1   3.0
 5. talada-abr1.elisa.ee                                                      0.0%    50    7.3   5.8   2.7   8.4   1.4
 6. talada-gw1.elisa.ee                                                       0.0%    50    5.4   5.7   2.4   9.7   1.5
 7. cloudflare.rtix.ee                                                        0.0%    50    7.8   7.4   2.7  21.1   3.3
 8. 104.18.5.237                                                              0.0%    50    4.0   5.9   2.3   9.1   1.5

`printf 'HEAD / HTTP/1.1\r\nHost: www.udacity.com\r\n\r\n' | nc www.udacity.com 80` - jooksutades seda commandi ei näidanud mu terminal mingit muutust ega vastet.

#### ping 8.8.8.8
Õiged vastused minu arvates oleks: Mu arvutil on interneti ühendus ning arvuti 8.8.8.8 töötab.

#### Ping versus HTTP
HTTP-ga on vaja teada mis veebi leheküljega sa soovid ühendust saada ning saadad selle HTTP GET requesti ning vastena saad tagasi veebilehe. Ping saadab requesti operatsioonisüsteemile mis saadab sealt tagsi vaste. Oluline Ping on lihtsam kui HTTP, aga HTTP ei kasuta Pingi.

#### Write your own HTTP to Wikipedia
Ma runisin `printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n' | nc en.wikipedia.org 80` ning sain vasteks: 
HTTP/1.1 301 Moved Permanently
content-length: 0
location: https://en.wikipedia.org/
server: HAProxy
x-cache: cp3066 int
x-cache-status: int-tls
connection: close
Mulle tundub antud olukorras, et lõpp punkt on pandud kinni, sellepärast pole ülesande all koodi ning annab mulle sellise vaste kui saadan requesti.

#### printf and netcat
printf on command mis annab lihtsalt stringi. Kui seda runida ilma muu commandita prindib ta selle järel oleva teksti õiges formaadis ehk kui sinna on lisatud newline ehk \n tuleb automaatselt newline.
nc on netcat mida kasutatakse interneti teenustega suhtlemiseks ehk kasutad, et suhelda serveriga või kasutada serverina.
| kasutatakse, et sellele eelneva vastet kasutada sellele järgneva sisestusena. 

#### What web server does Google use?
Runnisin `printf 'HEAD / HTTP/1.1\r\nHost: www.google.com\r\n\r\n' | nc www.google.com 80` ning vasteks sain:
HTTP/1.1 200 OK
Content-Type: text/html; charset=ISO-8859-1
Content-Security-Policy-Report-Only: object-src 'none';base-uri 'self';script-src 'nonce-mERLt0Qq769PCh-lFBNdxQ' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
Date: Mon, 08 Jan 2024 15:07:16 GMT
Server: gws
X-XSS-Protection: 0
X-Frame-Options: SAMEORIGIN
Transfer-Encoding: chunked
Expires: Mon, 08 Jan 2024 15:07:16 GMT
Cache-Control: private
Küsitud oli mis serverit Google kasutab ning üks rida on "Server: gws" ehk Google kasutab gws serverit mis peaks olema ta enda server.

#### printf and netcat illustrate layers
printf + nc saab kasutada HTTP päringuteks

Layer            Protocols             Concepts

Application      HTTP, SSH             URLs, passwords
Transport        TCP, UDP              Port numbers, sessions
Internet         IP                    IP addresses, routes
Hardware         Wifi, ethernet, DSL   signal strength, access points

Iga neist oleneb oma all olevast asjast ning annab asju mis aitavad ta üleval oleva töötamiseks. On ka erandeid näiteks: DNS, ping, ICMP, ARP.
Kõik asjad mis on üleval- või allpool IPst lähevad sealt läbi.

#### Listen on a Port
Runin commandi `man nc` mis avab terminalis netcati manuali. Selleks et nc kuulaks port 3456 runing `nc -l 3456`.

#### Waiting For Your Call
Port on nagu telefon, port ühendab arvutiga ning sellega tunneb mis server programm seal jookseb näiteks port 80 on http ning port 22 on ssh. Kui port ühendub mitte aktiivse arvutiga saab ta vastuseks errori RST (reset packet).

#### Listening and Connecting
Avasin kaks eraldi terminali ning mõlemas läksin vagranti. Ühes runisin `nc -l 1234` ning teises `nc localhost 1234` sain nende kahe vahel loodud ühenduse. Katkestamiseks kasutasin (ctrl+D) mille leidsin nc manualist. Proovisin algul ka (ctrl+Q) mis paneb mõningate asjade puhul, näiteks help commandi puhul selle lehe kinni.

#### Experiment with nc and HTTP
Kui ma runin näiteks `printf "GET / HTTP/2\r\n\r\n" | nc host.example.com 80` mille HTTP pole 1.1 annab ta mulle vea teate. Ehk siis kui ma saadan vale headeriga annab ta mulle vea teate ning ei toimi. Ma runisin ka `printf "GET / HTTP/1.1\r\nHost: www.example.com\r\n\r\n" | nc www.example.com 80` header on kõik enne html-i.

#### Port Numbers
Hakkasin järjest katsetama ette antud port numbreid kasutades `nc -l ****` ning `nc localhost ****` (**** on numbrid mida katsetan). Port 100 andis vastuseks "nc: Permission denied" ning localhost ei tee midagi. Port 9999 töötas. Port 10240 töötab samuti. Port 65535 töötab, aga sealt üles ei tööta enam, samuti huvi pärast proovisin kas natuke suurem töötab ehk katsetasin ka port 65536 ning see ei töötanud.
Edasi proovisin leida kõige väiksemat. Port 1, port 100 ning port 512 ei töötanud andes vastuseks "nc: Permission denied". Katsetasin samuti ka 1023, kuid see andis vastuseks "nc: Permission denied". Ei saa täpselt aru mille all mõeldakse "negative infinity". Proovisin ka port 0.000023 ning see andis vastuseks "nc: getaddrinfo: Servname not supported for ai_socktype".

#### Port Numbers Part Two
Kõik portid 1023 all on reserveeritud superuseritele või teisisõnu root. Kui tahta alustada web serverit port 80 tuleb seda teha sudo commandiga ning kui see tööle hakkab eemaldab oma ligipääsu rootile, süsteemi kaitseks. Kaks terminali samas arvutis ei saa kuulata samaaegselt sama porti ning annab vea teate.

#### One listening server per port
Pidin leidma kõik programmid mis hetkel kuulavad minu arvutit selleks runisin `sudo lsof -i` ning nendeks on:
sunrpc, ssh, port 34111 ning port 38654.

#### Be a Web Server
Runisin commandi `printf 'HTTP/1.1 302 Moved\r\nLocation: https://www.eff.org/' | nc -l 2345` ning internetis runisin enda IPv4 nii ethernet 2 ning ethernet 4 kui ka default gateway, kuid kõigil juhtudel sain errori "This site can’t be reached" oma ip leidsin kui runisin terminalis `ipconfig`. Tegelikult peaks ette tulema see lehekülg ning andma mulle ka teate terminalis selle kohta.

#### Outro
Kui arvuti üritab avada veebilehte, siis ta teeb mitut protsessi korraga enne kui leht ette tuleb. Ja serveril võib ka olla mittu kasutajat, kes on eri punktides ja aegades ning neile on vaja saata sama info ning ka vajadusel kuvada erinevat pilti. Programmil on mitu erinevat viisi kuidas ta korraga haldab mitut ühendust. Esimene on mida algsed serverid tegid ja mida näiteks ssh teeb. Kui uus ühendus tuleb teeb ta selle kaheks ning alustab uut ühendust. Teine variant on kuidas uuemad serverid teevad on teevad ühendus kogu mis tegeleb ühe ühendusega korraga ning see on kiirem kui alustada uuega, aga sellel on limiit palju saab neid korraga ühenduda. Kolmandaks on üks protsess mis vahetab kiirelt ühenduste vahel, näiteks epol linuxis.

### Names and Addresses

#### Intro
IPd kasutatakse selleks, et anda kindel punkt internetis mis on ainulaadne ühel ühendusel. Kõik mis internetis liigub on packet ning sellel on alati kaasas IP mis näitab kohta kust see tuli ning kuhu läheb. Host- on masin internetis mis haldab mingit teenust. Endpoints- kaks masinat või programmi mis suhtlevad omavahel.

#### Ping a Hostname
Ping jookseb seni kuni peatatakse "ctrl + C" `ping -c (number) (koht)` pingib seda nii mitu korda kui on sisestatud number. Runing `ping google.com` ning saan, et google IP on 216.58.211.238 See on suure tõenäosusega erinev, sest googlel on mitu erinevat arvutit, et vähendada töökoormust.

#### Intro to DNS
DNS (Domain Name System) muudab veebi lehe nimetuse IP aadressiks. DNSis on palju erinevaid protokolle, põhiline on A-Record mida kasutatakse et leida arvuti aadress internetist, tänu selle nimele. Kui luua veebilehte on vaja üles seada ka DNS, et inimesed kes seda lehte otsivad leiaksid selle oma arvutiga üles. DNS võib ka aeguda ning siis pole enam juurdepääsu sellele lehele. Ning sellele annab juurdepääasu The Resolver mis kõigis operatsiooni süsteemides.

#### The host command
hostiga saab otsida recordeid DNSist. `host -t a` on a tüüp mis otsib IPv4. Otsisin https://www.twitch.tv/ IP aadressi ning tulemuseks sain, et on 4 IPd:
twitch.tv has address 151.101.194.167
twitch.tv has address 151.101.2.167
twitch.tv has address 151.101.130.167
twitch.tv has address 151.101.66.167

#### Dig into DNS Records
Peale `host` commandi saab kasutada `dig` command mis näitab rohkem informatsiooni ning seda on parem kasutada scriptides. dig näitab lisaks serverit mis võttis vastu requesti ning millal see vastu võeti.

#### Research DNS Record Types
Peale a recordi on veel DNS record tüüpe. CNAME on canonical name. AAAA ("quad-A") on IPv6 aadressid. Ning NS on DNS name server. 

#### DNS is Distributed with Caching
.com jne on gtld - global top-level domain. Resolverid räägivad caching serveriga nt ruuter ning siis saadab edasi root serverisse ja sealt com serverisse ja sealt A record serverisse ning siis paneb selle mällu, et ei peaks uuesti otsima seda. DNSil on TTL see tähendab kaua neid mälus hoiab.

#### DNS and HTTP
Domeeni nimed on ka olulised HTTPle. SSL on kaitse süsteem mis krüpteerib info serveri ja browseri vahel, et teised ei saaks lugeda privaatset infot. Ning kontrollib kas lehekülg on see mis olema peaks mitte liba leht.

#### Subdomains and FQDNs
DNSi aadressid on sarnased, igal domeenil on oma server mis mõistab vastava domeeni lehekülgi. www. ei ole otseselt kohustuslik domeeni nimi. See on looja enda soov ja valik kuidas ta soovib oma domeeni. Firmadel on ühine otsingu domeen mis otsib sealsed asjad esimesena.

#### Register a Domain
Veebilehtedele saab ligi ka sisestades selle koha IP aadressi. domains.google.com on võimalik oma domeen luua. Ma ei soovinud raha eest endale domeeni teha, seega ma jätsin selle osa vahele.

#### 32 bits to 128 bits
On olemas nii IPv4 kui ka IPv6 aadressid. IPv4 kirjutatakse nelja numbriga ja numbrite vahel on punktid. Üks number on 1 byte ehk 8 bit-i. Ehk see number on 0-255.

#### 8 Bits in a Byte
1 byte võrdub ühe octetiga. Mõned arvutid kasutavad teises suuruses byte.

#### Bits
Nelja bitises suuruses on 16 erinevat väärtust. Iga uus bit duubeldab suuruse.

#### Bits in a Port Number
Port numbris on 16 biti. 

### Addressing and Networks

#### Special Addresses
IPv4 on 32 bitine, kõiki neid kasutata, sest osad on reserveeritud, umbes 1/8 on reserveeritud erinevateks puhkudeks ning neid ei saa niisama kasutada.

#### Reserved IP addresses
IPv4 lahterdatakse esimese numbri järgi 0, 10 ning 127 on reserveeritud. 192 on osaliselt reserveeritud. 224 on IP multicastiks. Ning alates 240 on kõik kaotatud ning blokeeritud, sest tehti vale arvestus. 1/16 IPv4 ei ole võimalik üldse kasutada.

#### How Many Addresses
Ma arvan et avalike IPv4 aadresseid on üle biljoni. Sest iga bit kahekordistab arve, siis 32 peaks tulema üle biljoni, aga kindlalt ei saa rohkem kui tähti.

#### Netblocks and Subnets
Kõik arvutid samas internetis algavad sama IPga. Need arvutid saavad suhelda omavahel ilma ruuterita. Arvutid võivad sattuda segadusse kui eri bitidega netid. 

#### Subnet Masks
Mõnikord kasutatakse subnet maske neti suuruseks. Neid kirjutatakse vaskalut alates olenevalt nii palju ühtesid ning ülejäänud 0 ehk siis 24 suurune oleks 255.255.255.0 oktetide kohapealt. 16 decimal subnet oleks 255.255.0.0

#### Stanford's /14
Stanfordi ülikooli network on /14 ehk esimene osa on kindlalt 171 ning teisest on ainult 6 biti täidetud ning ülejäänud 2 vabad samuti 16 biti peale seda. Subnet mask Stanfordil oleks 255.252.0.0 ehk siis oleks 18 biti hoste mis teeb umbes 250000 aadressi.

#### Not Enought Addresses
IPv4 aadresse on vähem kui inimesi ning suurfirmad võtavad endale rohkem aadresse kui vaja.

#### The Host is a Lie
IPd ei kuulu hostidele vaid interfaceidele. Nt Ethernet interface, Wifi interface, Loopback interface (räägib iseendaga). Loopback on peaaegu alati 127.0.0.1 ning sama localhost

#### Interfaces Quiz
Minu arvutil on kaks interfacei loopback mis on 127.0.0.1 ning ethernet mis on 10.0.2.15

#### Routers and Default Gateways
Ruuter ühendab erinevaid ipsid internetiga. Ehk kui erinevas local networkis olevad arvutid tahavad omavahel suhelda käib suhtlus läbi ruuteri.

#### Default Gateway Quiz
Runnisin `ip route show default` ning mu default on etherneti kaudu 10.0.2.2 ning siis ma pingisin enda aadressi, et teada saada kui kiiresti see pingib ning sain tulemuseks min/avg/max/mdev = 0.058/0.360/0.531/0.110 ms

#### NAT
IPv4 lahenduseks on, et ISP on andnud ühele hostile ühe IPv4 aadressi, põhimõtteliselt jätab ruuter mulje et ühes võrgus on üks arvuti. Kõik masinad ühes võrgus millel on privaatsed aadressid suhtlevad ruuteriga millel on üks avalik aadress ning see suhtleb ISPga. Privaat aadressid kasutavad ühte kolmest reserveeritud netblockist. NAT muudabki privaat aadressi avalikuks. NAT pole lahendus vähestele IPv4 aadressidele, aga võimaldab, et see töötaks

#### Private and Public
Veebilehed näevad ruuteri avaliku IPd aga ei näe privaat IPd mis on kõigil arvutitel unikaalne. Minu arvuti IP on 10.0.2.15 see aadress on privaatne. Minu avalik IP on 81.90.119.225

#### IPv6
On lahendus IPv4 vastu. Seal on 128 biti neli korda suurem kui IPv4. IPv6 ei kirjutata decimalis vaid hexadecimal. IPv6 kasutatakse rohkem kodus.

#### IPv6 Quiz
Läksin lehele "https://test-ipv6.com/" ning proovisin seda arvutis ning telefonis pole mul IPv6

### Protocol Layers

#### Intro: HTTP on TCP on IP
TCP lubab kahel programmil saata oma vahel infot. HTTP ehitatakse TCP peale ning see omakorda IP peale

#### The Protocaol Stack
Staki terminite vahel on palju sarnasusi aga need tähendavad erinevaid asju.

#### Watching Ping and DNS in tcpdump
tcpdumpil on oma keel. ma proovisin runnida ka `sudo tcpdump -n host 8.8.8.8`. Iga pingi kohat tuleb kaks packetit tcpdumpiga ehk üks mis läheb sinna ning üks mis tuleb vastu. Sellega saab ka jälgida DNSi.

#### Watching HTTP in tcpdump
Tegin kaks sessiooni ühes runisin `printf 'HEAD / HTTP/1.1\r\nHost: exmple.net\r\n\r\n' | nc example.net 80` ning teises `sudo tcpdump -n port 80` tulemuseks sain
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
16:13:00.235260 IP 10.0.2.15.33016 > 93.184.216.34.80: Flags [S], seq 3612204312, win 29200, options [mss 1460,sackOK,TS val 9518350 ecr 0,nop,wscale 6], length 0
16:13:00.336581 IP 93.184.216.34.80 > 10.0.2.15.33016: Flags [S.], seq 36672001, ack 3612204313, win 65535, options [mss 1460], length 0
16:13:00.336607 IP 10.0.2.15.33016 > 93.184.216.34.80: Flags [.], ack 1, win 29200, length 0
16:13:00.336748 IP 10.0.2.15.33016 > 93.184.216.34.80: Flags [P.], seq 1:38, ack 1, win 29200, length 37: HTTP: HEAD / HTTP/1.1
16:13:00.336779 IP 10.0.2.15.33016 > 93.184.216.34.80: Flags [F.], seq 38, ack 1, win 29200, length 0
16:13:00.336934 IP 93.184.216.34.80 > 10.0.2.15.33016: Flags [.], ack 38, win 65535, length 0
16:13:00.336940 IP 93.184.216.34.80 > 10.0.2.15.33016: Flags [.], ack 39, win 65535, length 0
16:13:00.439349 IP 93.184.216.34.80 > 10.0.2.15.33016: Flags [P.], seq 1:134, ack 39, win 65535, length 133: HTTP: HTTP/1.1 404 Not Found
16:13:00.439371 IP 10.0.2.15.33016 > 93.184.216.34.80: Flags [.], ack 134, win 30016, length 0
16:13:00.439381 IP 93.184.216.34.80 > 10.0.2.15.33016: Flags [F.], seq 134, ack 39, win 65535, length 0
16:13:00.439385 IP 10.0.2.15.33016 > 93.184.216.34.80: Flags [.], ack 135, win 30016, length 0

#### Analyzing the tcpdump data
Enne kui saadetakse infot luuakse ühendus omavahel ning ei saadeta midagi samuti peale seda kui infot on saadetud võetakse ühendus omavahel maha

#### tcpdump packet quiz
Minu arvutisse tuli teine variant, sest seal on näha nende ip > minu ip ning samuti on seal length taga ka mingi suurus. 

#### Sequence Diagrams
Kasutatakse, et demonstreerida neti protokolle. Aeg liigub ülevalt alla. Taskid tehakse ühe kaupa, enne üks kui alustatakse järgmisega. Ning üks request võib olla mitu edasi tagasi suhtlust.

#### Buffering
Paning pusle kokku ning sain vastuseks The password is always swordfish.

#### TCP Flags
SYN (synchronize) [S] — Alustab uut sessiooni.
FIN (finish) [F] — Lõpetab sessiooni. Saatja ütleb et ei saada enam infot.
PSH (push) [P] — Lõpetab HTTP requesti
RST (reset) [R] — TCP error
ACK (acknowledge) [.] — Nätab et saatja sai info kätte. Peaaegu iga packet v.a SYN kasutab seda lippu
URG (urgent) [U] — Tähtis info mida tuleb saata korrapäratus järjekorras.

#### Why do packets drop
Et ei tekiks olukorda kus info liikumisel tekib tuupik

#### TCP Errors
Runnin ühes terminalis `sudo tcpdump port 12345` ning teises `nc www.udacity.com 12345` ning tulemuseks sain, et ta saadab mitu packetit järjest aeglasemalt ning siis paneb nc kinni.

#### Timeouts
TCP on plaju taimereid mille täitumisel annab veateate. On ka olemas timeout sellele kui saaja pole infot kätte saanud.

#### Errors Quiz
TCP timeoutid toimuvad kui host lõpetab töötamise, ISPga kaob ühendus ning kui üritad ühendust saada serveriga mis ei eksisteeri.

### Big Networks

#### Hops: Not Just for Beer
Hop on info liikumine ühest kohast teise. Näiteks Cloudis ühelt ruuterilt teisele. Seda saab jälgida näiteks `traceroute` commandiga. `mtr` on arenunum. Proovisin ise `traceroute youtube.com`
traceroute to youtube.com (216.58.211.238), 30 hops max, 60 byte packets
 1  10.0.2.2 (10.0.2.2)  0.062 ms  0.046 ms  0.097 ms
 2  * * *
 3  * * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *

#### The Story about Ping
Ping jälgib ka kui palju aega kulub saadetud info sinna ning tagasi jõudmiseks.


#### The Story about Traceroute
van Jackobson avastas, et tunnus mis takistab ringi keerlemist saab kasutada traceroutiks. See töötab nii et saadetakse järejst suureneva TTL(Time to Live) ning nii leiab kõige kiirema tee läbi.

#### Bandwith across Networks
Bandwith on kiirus mis on ruuterite vahel. Palju aeglaseid annab kokku kiire ehk siis liidetakse neti kiirused kokku ning see loeb kiiremate netide puhul näiteks serverid kes peavadki võttma vastu väga palju liiklust.

#### What is Fast Anyway
Bandwith ning latency mõlemad määravad neti kiirust, näiteks madal bandwith ning kõrge latency on mõlemad aegalsed. Kõrge latency on kui info peab liikuma pikki vahemaid. Madal bandwith on kui arvuti on aeglane näiteks on väga vana. Bandwith mängib rolli ka siis kui laetavad failid on suured.

#### Bandwidth-Delay Product
Data mis Kuu ning Maa vahel oleks 1.28 Gbit igal aja hetkel.

#### Congestion
Selleks et Kuuga saaks suhelda oleks veel vaja saata infot aeglasemalt kui packetid dropivad.

#### Middleboxes 1: Firewalls & Filtering
Tulemüürid filtreerivad liiklust mis tuleb sisse ning läheb välja sellest võrgust kus nad on. Vahekastid saavad ka muuta seda sisse või välja minevat infot.

#### What can a filter do?
Et uurida miks info ei liigu korrektselt tuleks uurida mitut asja:
Kas on võimalik kasutajal pingida aadressi kuhu ta ligi ei pääse
Kas kasutaja saab ligi mõnele teisele domeenile samas serveris
Kas nad saavad otsida serveri nime `host` või `dig` commandiga
Ning kas kõik kasutajad kellel on sarnane probleem asuvad ühes riigis

#### Middleboxes: Proxies & NAT
Kodu ruuter toimib samaaegselt NAT ning lihtsa tulemüürina. ISPdel on oma klientide jaoks samuti välja töötatud oma NAT süsteem.

#### Users behind a NAT
Veebilehed eristavad kasutajaid sama public IDga:
Nende sisse logitud kasutaja nimega
Ning nende sessionis loodud küpsistega
