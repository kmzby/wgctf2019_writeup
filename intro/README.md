1. 25 кадр в промо ролике
2. В pdf с регламентом (были удивлены)
3. ```bash
$ dig any ctf2019.rocks

; <<>> DiG 9.10.6 <<>> any ctf2019.rocks
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 44438
;; flags: qr rd ra; QUERY: 1, ANSWER: 15, AUTHORITY: 0, ADDITIONAL: 10

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;ctf2019.rocks.			IN	ANY

;; ANSWER SECTION:
ctf2019.rocks.		196	IN	A	92.223.20.126
ctf2019.rocks.		196	IN	A	92.223.20.57
ctf2019.rocks.		300	IN	NS	ns2dhj.name.com.
ctf2019.rocks.		300	IN	NS	ns4hmp.name.com.
ctf2019.rocks.		300	IN	NS	ns1bqx.name.com.
ctf2019.rocks.		300	IN	NS	ns3sxz.name.com.
ctf2019.rocks.		300	IN	SOA	ns1bqx.name.com. support.name.com. 1568851200 10800 3600 604800 3600
ctf2019.rocks.		300	IN	MX	20 mx7.name.com.
ctf2019.rocks.		300	IN	MX	60 mx5.name.com.
ctf2019.rocks.		300	IN	MX	40 mx8.name.com.
ctf2019.rocks.		300	IN	MX	10 mx4.name.com.
ctf2019.rocks.		300	IN	MX	50 mx3.name.com.
ctf2019.rocks.		300	IN	MX	30 mx6.name.com.
ctf2019.rocks.		300	IN	TXT	"v=spf1 a mx ~all"
ctf2019.rocks.		300	IN	SRV	10 10 1337 getflag.ctf2019.rocks.

;; ADDITIONAL SECTION:
ns2dhj.name.com.	166	IN	A	162.88.60.47
ns2dhj.name.com.	60061	IN	AAAA	2600:2000:1000::47
ns4hmp.name.com.	166	IN	A	162.88.60.49
ns4hmp.name.com.	69180	IN	AAAA	2600:2000:1000::49
ns1bqx.name.com.	156	IN	A	162.88.61.47
ns1bqx.name.com.	64022	IN	AAAA	2600:2000:1001::47
ns3sxz.name.com.	156	IN	A	162.88.61.49
ns3sxz.name.com.	77454	IN	AAAA	2600:2000:1001::49
getflag.ctf2019.rocks.	300	IN	A	79.137.74.224

$ dig any flag.ctf2019.rocks 

; <<>> DiG 9.10.6 <<>> any flag.ctf2019.rocks 
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16668
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;flag.ctf2019.rocks.		IN	ANY

;; ANSWER SECTION:
flag.ctf2019.rocks.	300	IN	TXT	"WGCTF{A2EA873AA5C0F949D0666DA1252E2F62}"

;; Query time: 83 msec
;; SERVER: 10.128.41.11#53(10.128.41.11)
;; WHEN: Mon Sep 23 16:35:59 +03 2019
;; MSG SIZE  rcvd: 99

;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 58480
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;162.88.60.47.			IN	ANY

;; AUTHORITY SECTION:
.			900	IN	SOA	a.root-servers.net. nstld.verisign-grs.com. 2019092300 1800 900 604800 86400

;; Query time: 34 msec
;; SERVER: 10.128.41.11#53(10.128.41.11)
;; WHEN: Mon Sep 23 16:35:59 +03 2019
;; MSG SIZE  rcvd: 116
```
4. ```bash
$ nc getflag.ctf2019.rocks 1337
GETFLAG
WGCTF{2B3F2CFBBB5991A5F06D0DEF711C422A}
```
