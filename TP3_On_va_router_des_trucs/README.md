# TP3 : On va router des trucs
# I. ARP
### 1. Echange ARP

```
[elbatista@mokthar ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e2:df:8e brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.12/24 brd 10.3.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fee2:df8e/64 scope link
       valid_lft forever preferred_lft forever
[elbatista@mokthar ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=64 time=0.563 ms
*^C
--- 10.3.1.11 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.563/0.563/0.563/0.000 ms
[elbatista@mokthar ~]$ ip n s
10.3.1.11 dev enp0s8 lladdr 08:00:27:f1:9b:3c DELAY
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 REACHABLE
```

```
[elbatista@mohammed ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:f1:9b:3c brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.11/24 brd 10.3.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fef1:9b3c/64 scope link
       valid_lft forever preferred_lft forever
[elbatista@mohammed ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.547 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.824 ms
^C
--- 10.3.1.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1061ms
rtt min/avg/max/mdev = 0.547/0.685/0.824/0.138 ms
[elbatista@mohammed ~]$ ip n s
10.3.1.12 dev enp0s8 lladdr 08:00:27:e2:df:8e REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 REACHABLE
```

### 2. Analyse de trames

```
[elbatista@mohammed ~]$ sudo ip -s -s neigh flush all
10.3.1.12 dev enp0s8 lladdr 08:00:27:e2:df:8e ref 1 used 21/17/17 probes 1 REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 ref 1 used 66/0/66 probes 4 REACHABLE

*** Round 1, deleting 2 entries ***
*** Flush is complete after 1 round ***
[elbatista@mohammed ~]$ sudo tcpdump -i enp0s8 -w tp3_arp.pcapng "arp"
dropped privs to tcpdump
tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), snapshot length 262144 bytes
^C2 packets captured
2 packets received by filter
0 packets dropped by kernel
```
Pour avoir la visualisation de l'analyse des trames.

[capture tp3 ARP](./tp3_arp3.pcapng)


# II. Routage

### Activer le routage sur le noeud router

```
[elbatista@router ~]$ sudo firewall-cmd --add-masquerade --zone=public --permanent
success
[elbatista@router ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8 enp0s9
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: yes
  masquerade: yes
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```
### Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping
```
[elbatista@mohammed ~]$ sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev
 enp0s8
[sudo] password for elbatista:
[elbatista@mohammed ~]$ ip r s
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 100
10.3.2.0/24 via 10.3.1.254 dev enp0s8
[elbatista@mohammed ~]$ cd /etc/sysconfig/network-scripts/
[elbatista@mohammed network-scripts]$ sudo nano enp0s8
[elbatista@mohammed network-scripts]$ ip r s
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 100
10.3.2.0/24 via 10.3.1.254 dev enp0s8
```

```
[elbatista@mokthar ~]$  sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev
 enp0s8
[sudo] password for elbatista:
[elbatista@mokthar ~]$ ip r s
10.3.1.0/24 via 10.3.2.254 dev enp0s8
10.3.2.0/24 dev enp0s8 proto kernel scope link src 10.3.2.12 metric 100
[elbatista@mokthar ~]$ cd /etc/sysconfig/network-scripts/
[elbatista@mokthar network-scripts]$ sudo nano enp0s8
[elbatista@mokthar network-scripts]$ ip r s
10.3.1.0/24 via 10.3.2.254 dev enp0s8
10.3.2.0/24 dev enp0s8 proto kernel scope link src 10.3.2.12 metric 100
```
```
[elbatista@mokthar /]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=63 time=0.709 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=63 time=1.82 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=63 time=1.73 ms
64 bytes from 10.3.1.11: icmp_seq=4 ttl=63 time=1.94 ms
^C
--- 10.3.1.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3024ms
rtt min/avg/max/mdev = 0.709/1.551/1.943/0.492 ms
```

## 2. Analyse de trames

### Analyse des échanges ARP

| ordre | type trame | IP source | MAC source | IP destination | MAC destination |
| ----- | ---------- | --------- | ---------- | -------------- | --------------- |
| 1     |Requête ARP| | 08:00:27:f1:9b:3c |  | Broadcast FF:FF:FF:FF:FF |
| 2     |    Réponse ARP        |          |  08:00:27:4F:98:65           |                | 08:00:27:f1:9b:3c |
| 3     |     ping       |      10.3.1.11     |     08:00:27:f1:9b:3c       |     10.3.2.12           |     08:00:27:4F:98:65             |
| 4     |          pong  |      10.3.2.12     |  08:00:27:4F:98:65           |      10.3.1.11          |             08:00:27:f1:9b:3c    |

[capture TP3 routage marcel](.\tp3_routage_marcel.pcapng.pcap)


## 3. Accès internet

### Donnez un accès internet à vos machines

```
[elbatista@mohammed ~]$ sudo ip route add default via 10.3.1.254 dev enp
0s8
[sudo] password for elbatista:
[elbatista@mohammed ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=22.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=23.7 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 22.137/22.920/23.703/0.783 ms
```

```
[elbatista@mohammed ~]$ traceroute 1.1.1.1
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 60 byte packets
 1  _gateway (10.3.1.254)  0.405 ms  0.380 ms  0.547 ms
 2  10.0.2.2 (10.0.2.2)  0.703 ms  0.687 ms  0.669 ms
 3  10.0.2.2 (10.0.2.2)  6.630 ms  6.613 ms  6.579 ms
[elbatista@mokthar ~]$ ping google.com
PING google.com (216.58.214.78) 56(84) bytes of data.
64 bytes from par10s39-in-f14.1e100.net (216.58.214.78): icmp_seq=1 ttl=112 time=21.8 ms
64 bytes from fra15s10-in-f14.1e100.net (216.58.214.78): icmp_seq=2 ttl=112 time=23.3 ms
64 bytes from par10s39-in-f14.1e100.net (216.58.214.78): icmp_seq=3 ttl=112 time=22.7 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 21.764/22.602/23.339/0.647 ms
[elbatista@mokthar ~]$ sudo /etc/resolv.conf
```

### Analyse de trames

```
[elbatista@mohammed ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=25.9 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=26.9 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=112 time=25.6 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1998ms
rtt min/avg/max/mdev = 25.569/26.131/26.877/0.549 ms
```


| ordre | type trame | IP source | MAC source   |IP destination  | MAC destination |
| -------- | -------- | --- | --- | --- | -------- |
|   1       |     ping     |  10.3.1.11   |  08:00:27:f1:9b:3c   |   8.8.8.8  |      08:00:27:4f:98:65    |
| 2    | pong     |    8.8.8.8   |   08:00:27:4f:98:65  |  10.3.1.11   |  08:00:27:f1:9b:3c     |

[capture tp3 routage internet](./tp3_routage_internet1.pcapng.pcap)


# III. DHCP

## 1. Mise en place du serveur DHCP
 
### Sur la machine john, vous installerez et configurerez un serveur DHCP

```
[elbatista@mohammed network-scripts]$ sudo dnf install dhcp-server -y
[elbatista@mohammed network-scripts]$ sudo cat /etc/dhcp/dhcpd.conf
[sudo] password for elbatista:
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
default-lease-time 900;
max-lease-time 10800;

authoritative;

subnet 10.3.1.0 netmask 255.255.255.0 {
range 10.3.1.1 10.3.1.253;
}
[elbatista@mohammed network-scripts]$
[elbatista@mohammed network-scripts]$ sudo firewall-cmd --add-port=67/udp --permanent
success
[elbatista@mohammed network-scripts]$ sudo firewall-cmd --reload
success
[elbatista@mohammed network-scripts]$ sudo systemctl enable --now dhcpd
```

```
[elbatista@bob ~]$ cd /etc/sysconfig/network-scripts
[elbatista@bob network-scripts]$ sudo nano ifcfg-enp0s8
[sudo] password for elbatista:
[elbatista@bob network-scripts]$ sudo nmcli con reload
[elbatista@bob network-scripts]$ sudo nmcli con up "System enp0s8"
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/2)
[elbatista@bob network-scripts]$ sudo systemctl restart NetworkManager
[elbatista@bob network-scripts]$ sudo cat ifcfg-enp0s8
DEVICE=enp0s8

BOOTPROTO=dhcp
ONBOOT=yes
[elbatista@localhost network-scripts]$ ip a
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:ec:c5:49 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.2/24 brd 10.3.1.255 scope global dynamic noprefixroute enp0s8
       valid_lft 874sec preferred_lft 874sec
    inet6 fe80::a00:27ff:feec:c549/64 scope link
       valid_lft forever preferred_lft forever
```

### Améliorer la configuration du DHCP

```
[elbatista@mohammed network-scripts]$ sudo cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
default-lease-time 900;
max-lease-time 10800;

authoritative;

subnet 10.3.1.0 netmask 255.255.255.0 {
range 10.3.1.1 10.3.1.200;
option routers 10.3.1.254;
option subnet-mask 255.255.255.0;
option domain-name-servers 8.8.8.8;
}
[elbatista@bob ~]$ sudo nmcli con down "System enp0s8"
[sudo] password for elbatista:
Connection 'System enp0s8' successfully deactivated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/15)
[elbatista@bob ~]$ sudo nmcli con up "System enp0s8"
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/17)
[elbatista@bob ~]$ ip a
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:ec:c5:49 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.2/24 brd 10.3.1.255 scope global dynamic noprefixroute enp0s8
       valid_lft 721sec preferred_lft 721sec
    inet6 fe80::a00:27ff:feec:c549/64 scope link
       valid_lft forever preferred_lft forever
```
```
[elbatista@bob ~]$ ping 10.3.1.254
PING 10.3.1.254 (10.3.1.254) 56(84) bytes of data.
64 bytes from 10.3.1.254: icmp_seq=1 ttl=64 time=0.322 ms

64 bytes from 10.3.1.254: icmp_seq=2 ttl=64 time=0.230 ms
^C
--- 10.3.1.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1024ms
rtt min/avg/max/mdev = 0.230/0.276/0.322/0.046 ms
```

```
[elbatista@bob ~]$ ip r s
default via 10.3.1.254 dev enp0s8 proto dhcp src 10.3.1.2 metric 100
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.2 metric 100
[elbatista@bob ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=0.653 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=0.624 ms
^C
--- 10.3.2.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1034ms
rtt min/avg/max/mdev = 0.624/0.638/0.653/0.014 ms
```
```
[elbatista@bob ~]$ dig gitlab.com

; <<>> DiG 9.16.23-RH <<>> gitlab.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31255
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;gitlab.com.                    IN      A

;; ANSWER SECTION:
gitlab.com.             209     IN      A       172.65.251.78

;; Query time: 26 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Fri Oct 28 12:11:25 CEST 2022
;; MSG SIZE  rcvd: 55


[elbatista@bob ~]$ ping google.com
PING google.com (142.250.179.110) 56(84) bytes of data.
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=1 ttl=247 time=23.2 ms
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=2 ttl=247 time=22.8 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 22.824/23.008/23.192/0.184 ms
[elbatista@bob ~]$
```

### 2. Analyse de trames

```
[elbatista@bob ~]$ sudo dnf install -y dhclient
[elbatista@bob ~]$ sudo dhclient -v  -r enp0s8
[elbatista@bob ~]$ sudo dhclient -v  enp0s8
```

l'IP fournie au client est 10.3.1.3
l'adresse IP de la passerelle est 10.3.1.254
l'adresse du serveur DNS que vous proposez au client est 8.8.8.8.

[capture tp3 DHCP](.\tp3_dhccp.pcap.pcap)
