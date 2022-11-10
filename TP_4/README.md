# TP4 : TCP, UDP et services réseau

## I. First steps
- Pour Discord

```
PS C:\Users\lucas> netstat -b -n
Connexions actives

  Proto  Adresse locale         Adresse distante       État
 TCP    10.33.17.12:55013      162.159.133.234:443    ESTABLISHED
 [Discord.exe]
```
[capture discord](discord.pcap.pcapng)

- Pour FallGuys

```
PS C:\Users\lucas> netstat -n -a -p udp

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  UDP    0.0.0.0:58346          188.42.41.6:7877         
```

[capture FallGuys](FallGuys.pcap.pcapng)

   - Pour Instagram

```
PS C:\Users\lucas> netstat -n -a -p udp

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  UDP    0.0.0.0:50152          81.65.32.162:443     
[msedge.exe]
```

[capture instagram](instagrma.pcap.pcapng)


- Pour Zoom

```
PS C:\Users\lucas> netstat -n -a -p udp

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  UDP    0.0.0.0:60727          206.247.74.210:8801
  [Zoom.exe]
```

[capture Zoom](Zoom.pcap.pcapng)


- PourEdge 

```
PS C:\Users\lucas> netstat -b -n

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  TCP    10.33.17.12:63046      99.83.179.177:443      ESTABLISHED
 [msedge.exe]
```
[capture edge](DNS.pcapng)

## II. Mise en place

### 1. SSH

###  Examinez le trafic dans Wireshark

[capture ssh](ssh.pcap.pcapng)

### Demandez aux OS 
```
lucas@LAPTOP-GEJ2DKFJ MINGW64 ~
$ netstat -b -n

Connexions actives

  Proto  Adresse locale         Adresse distante       ▒tat
  TCP    10.4.1.1:56346         10.4.1.11:22           ESTABLISHED
 [ssh.exe]
```
## III. DNS

### 2. Setup

### Dans le rendu, je veux

```
[elbatista@dns-server ~]$ sudo cat /etc/named.conf
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };

        recursion yes;

        dnssec-validation yes;

        managed-keys-directory "/var/named/dynamic";
        geoip-directory "/usr/share/GeoIP";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "tp4.b1" IN {
     type master;
     file "tp4.b1.db";
     allow-update { none; };
     allow-query {any; };
};
zone "1.4.10.in-addr.arpa" IN {
     type master;
     file "tp4.b1.rev";
     allow-update { none; };
     allow-query { any; };
};
```

```
[elbatista@dns-server ~]$ sudo cat /var/named/tp4.b1.db
TTL 86400
@ IN SOA dns-server.tp4.b1. admin.tp4.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

@ IN NS dns-server.tp4.b1.

dns-server IN A 10.4.1.201
node1      IN A 10.4.1.11
[elbatista@dns-server ~]$
```

```
[elbatista@dns-server ~]$ sudo cat /var/named/tp4.b1.rev
TTL 86400
@ IN SOA dns-server.tp4.b1. admin.tp4.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

@ IN NS dns-server.tp4.b1.

201 IN PTR dns-server.tp4.b1.
11 IN PTR node1.tp4.b1.
```

```
[elbatista@dns-server ~]$  systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
     Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; ve>
     Active: active (running) since Thu 2022-11-10 10:40:27 CET; 5min a>
   Main PID: 1393 (named)
      Tasks: 6 (limit: 5904)
     Memory: 23.7M
        CPU: 79ms
     CGroup: /system.slice/named.service
             └─1393 /usr/sbin/named -u named -c /etc/named.conf

Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: configuring command chan>
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: command channel listenin>
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: configuring command chan>
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: command channel listenin>
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: managed-keys-zone: loade>
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: zone 1.4.10.in-addr.arpa>
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: zone tp4.b1/IN: loaded s>
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: all zones loaded
Nov 10 10:40:27 dns-server.tp4.b1 named[1393]: running
Nov 10 10:40:27 dns-server.tp4.b1 systemd[1]: Started Berkeley Internet>
lines 1-20/20 (END)
```

```
[elbatista@dns-server ~]$ sudo ss -ltunp
Netid      State       Recv-Q       Send-Q             Local Address:Port             Peer Address:Port      Process
udp        UNCONN      0            0                     10.4.1.201:53                    0.0.0.0:*          users:(("named",pid=1548,fd=34))
udp        UNCONN      0            0                     10.4.1.201:53                    0.0.0.0:*          users:(("named",pid=1548,fd=35))
udp        UNCONN      0            0                      10.0.2.15:53                    0.0.0.0:*          users:(("named",pid=1548,fd=30))
udp        UNCONN      0            0                      10.0.2.15:53                    0.0.0.0:*          users:(("named",pid=1548,fd=31))
udp        UNCONN      0            0                      127.0.0.1:53                    0.0.0.0:*          users:(("named",pid=1548,fd=24))
udp        UNCONN      0            0                      127.0.0.1:53                    0.0.0.0:*          users:(("named",pid=1548,fd=25))
udp        UNCONN      0            0                          [::1]:53                       [::]:*          users:(("named",pid=1548,fd=39))
udp        UNCONN      0            0                          [::1]:53                       [::]:*          users:(("named",pid=1548,fd=38))
tcp        LISTEN      0            10                    10.4.1.201:53                    0.0.0.0:*          users:(("named",pid=1548,fd=36))
tcp        LISTEN      0            10                    10.4.1.201:53                    0.0.0.0:*          users:(("named",pid=1548,fd=37))
tcp        LISTEN      0            10                     10.0.2.15:53                    0.0.0.0:*          users:(("named",pid=1548,fd=32))
tcp        LISTEN      0            10                     10.0.2.15:53                    0.0.0.0:*          users:(("named",pid=1548,fd=33))
tcp        LISTEN      0            10                     127.0.0.1:53                    0.0.0.0:*          users:(("named",pid=1548,fd=26))
tcp        LISTEN      0            10                     127.0.0.1:53                    0.0.0.0:*          users:(("named",pid=1548,fd=27))
tcp        LISTEN      0            128                      0.0.0.0:22                    0.0.0.0:*          users:(("sshd",pid=718,fd=3))
tcp        LISTEN      0            4096                   127.0.0.1:953                   0.0.0.0:*          users:(("named",pid=1548,fd=23))
tcp        LISTEN      0            10                         [::1]:53                       [::]:*          users:(("named",pid=1548,fd=41))
tcp        LISTEN      0            10                         [::1]:53                       [::]:*          users:(("named",pid=1548,fd=40))
tcp        LISTEN      0            128                         [::]:22                       [::]:*          users:(("sshd",pid=718,fd=4))
tcp        LISTEN      0            4096                       [::1]:953                      [::]:*          users:(("named",pid=1548,fd=42))
```

### Ouvrez le bon port dans le firewall

```
[elbatista@dns-server ~]$ sudo firewall-cmd --add-port=53/udp --permanent
success
[elbatista@dns-server ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

## 3. Test

### Sur la machine node1.tp4.b1

``` 
[elbatista@node1 ~]$ sudo cat /etc/resolv.conf
[sudo] password for elbatista:
# Generated by NetworkManager
nameserver 10.4.1.201
```

``` 
[elbatista@node1 ~]$ ping node1.tp4.b1
PING node1.tp4.b1 (10.4.1.11) 56(84) bytes of data.
64 bytes from node1.tp4.b1 (10.4.1.11): icmp_seq=1 ttl=64 time=0.064 ms
64 bytes from node1.tp4.b1 (10.4.1.11): icmp_seq=2 ttl=64 time=0.071 ms
64 bytes from node1.tp4.b1 (10.4.1.11): icmp_seq=3 ttl=64 time=0.078 ms
^C
--- node1.tp4.b1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2062ms
rtt min/avg/max/mdev = 0.064/0.071/0.078/0.005 ms
```

```
[elbatista@node1 ~]$ ping dns-server.tp4.b1
PING dns-server.tp4.b1 (10.4.1.201) 56(84) bytes of data.
64 bytes from dns-server.tp4.b1 (10.4.1.201): icmp_seq=1 ttl=64 time=0.206 ms
64 bytes from dns-server.tp4.b1 (10.4.1.201): icmp_seq=2 ttl=64 time=0.973 ms
^C
--- dns-server.tp4.b1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1057ms
rtt min/avg/max/mdev = 0.206/0.589/0.973/0.383 ms
```

### Sur votre PC

```
PS C:\Users\lucas> nslookup node1.tp4.b1
Serveur :   dns-server.tp4.b1
Address:  10.4.1.201

Nom :    node1.tp4.b1
Address:  10.4.1.11
```
[capture DNS](DNS.pcapng)