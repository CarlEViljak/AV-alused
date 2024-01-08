# AV-alused

## Networking for Web Developers

### From Ping to HTTP

#### Setting Up For Thsi Course
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

### Names and Addresses

### Addressing and Networks

### Protocol Layers

### Big Networks
