# TP2 : Ethernet, IP, et ARP

# I. Setup IP
## Mettez en place une configuration réseau fonctionnelle entre les deux machines

Sur la VM 
```
batista@batista-VirtualBox:~$ ls /etc/netplan/01-network-manager-all.yaml
/etc/netplan/01-network-manager-all.yaml
batista@batista-VirtualBox:~$ sudo nano /etc/netplan/01-network-manager-all.yaml
  GNU nano 6.2               /etc/netplan/01-network-manager-all.yaml                        # Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp0s8:
      addresses: [10.31.12.225/22]
      dhcp4: false
      dhcp6: false
    enp0s3:
      dhcp4: true
  version: 2
batista@batista-VirtualBox:~$ sudo netplan apply  
```
```
batista@batista-VirtualBox:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:67:1f:d7 brd ff:ff:ff:ff:ff:ff
    inet 10.31.12.225/22 brd 10.31.15.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe67:1fd7/64 scope link
       valid_lft forever preferred_lft forever
```
Sur mon PC 

```
PS C:\Users\lucas> ipconfig

Carte Ethernet VirtualBox Host-Only Network :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::cca4:b156:342c:4617%4
   Adresse IPv4. . . . . . . . . . . . . .: 10.31.12.224
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Passerelle par défaut. . . . . . . . . :
```

## Prouvez que la connexion est fonctionnelle entre les deux machines

```
batista@batista-VirtualBox:~$ ping 10.31.12.224
PING 10.31.12.224 (10.31.12.224) 56(84) bytes of data.
64 bytes from 10.31.12.224: icmp_seq=1 ttl=128 time=0.472 ms
64 bytes from 10.31.12.224: icmp_seq=2 ttl=128 time=0.860 ms
64 bytes from 10.31.12.224: icmp_seq=3 ttl=128 time=0.805 ms
^C
--- 10.31.12.224 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 0.472/0.712/0.860/0.171 ms
```
```
PS C:\Users\lucas> ping 10.31.12.225

Envoi d’une requête 'Ping'  10.31.12.225 avec 32 octets de données :
Réponse de 10.31.12.225 : octets=32 temps<1ms TTL=64
Réponse de 10.31.12.225 : octets=32 temps=1 ms TTL=64
Réponse de 10.31.12.225 : octets=32 temps=1 ms TTL=64
Réponse de 10.31.12.225 : octets=32 temps=1 ms TTL=64

Statistiques Ping pour 10.31.12.225:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 1ms, Moyenne = 0ms

```

## Wireshark it

Après avoir envoyer un `ping` depuis les deux machines. il en ressort ce PCAP et nous voyons que l'on envoie un `request` et que l'on reçoit un `reply`

[capture PCAP ping](pcap_ping1.pcapng)


# II. ARP my bro

## Check the ARP table
Pour l'adresse MAC de la VM
```
PS C:\Users\lucas> arp -a

Interface : 10.31.12.224 --- 0x4
  Adresse Internet      Adresse physique      Type
  10.31.12.225          08-00-27-67-1f-d7     dynamique
  10.31.15.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```

Pour la MAC du reseau du train dans lequelle je suis.

```
PS C:\Users\lucas> arp -a

Interface : 10.23.3.62 --- 0x8
  Adresse Internet      Adresse physique      Type
  10.23.0.1             00-21-21-21-21-11     dynamique
  10.23.7.255           ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```

##  Manipuler la table ARP

Vider la table ARP (en tant qu'administrateur)
```
PS C:\WINDOWS\system32> arp -d
PS C:\WINDOWS\system32> arp -a

Interface : 10.31.12.224 --- 0x4
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.23.3.62 --- 0x8
  Adresse Internet      Adresse physique      Type
  10.23.0.1             00-21-21-21-21-11     dynamique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
```

Après ping 

```
PS C:\WINDOWS\system32> arp -a

Interface : 10.31.12.224 --- 0x4
  Adresse Internet      Adresse physique      Type
  10.31.12.225          08-00-27-67-1f-d7     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.23.3.62 --- 0x8
  Adresse Internet      Adresse physique      Type
  10.23.0.1             00-21-21-21-21-11     dynamique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```
## Wireshark it

* Lorsqu'un message est envoyer pour la première fois entre deux machine, la première machine demande si il y a quelqu'un dans le réseaux qui utilise l'adresse IP mis dans le ping(par exemple). 
* Celui qui l'utilise repond et donne son adresse MAC. 

[capture ARP](ARP.pcapng)

# III. DHCP you too my brooo

## Wireshark it

Lors de la capture nous arivons à identifier 4 trames.
1. Discover
2. Offer
3. Resquet
4. Acknowledge


l'adresse IP que l'on me propose est donc 10.23.3.62 
l'adresse IP de la passerelle du réseau est 10.23.0.1
l'adresse d'un serveur DNS joignable depuis ce réseau est donc 10.23.0.1

[capture DORA](DORA1.pcapng)
