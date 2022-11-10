# TP1 - Premier pas réseau

# I. Exploration locale en solo

## 1. Affichage d'informations sur la pile TCP/IP locale

### Affichez les infos des cartes réseau de votre PC

```
PS C:\Users\lucas> ipconfig /all

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Intel(R) Wi-Fi 6 AX201 160MHz
   Adresse physique . . . . . . . . . . . : C4-BD-E5-A7-5B-3E
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::c5ba:3ca0:a6eb:1119%8(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.16.198(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Bail obtenu. . . . . . . . . . . . . . : lundi 3 octobre 2022 08:54:48
   Bail expirant. . . . . . . . . . . . . : mardi 4 octobre 2022 08:54:48
   Passerelle par défaut. . . . . . . . . : 10.33.19.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.19.254
   IAID DHCPv6 . . . . . . . . . . . : 80002533
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2A-BC-B9-32-C4-BD-E5-A7-5B-3E
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       8.8.4.4
                                       1.1.1.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
     
```
Et je n'ai pas de prise ethernet donc pas de carte Ethernet

### Affichez votre gateway

```
PS C:\Users\lucas> ipconfig

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::c5ba:3ca0:a6eb:1119%8
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.16.198
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Passerelle par défaut. . . . . . . . . : 10.33.19.254
```
### Déterminer la MAC de la passerelle

```
PS C:\Users\lucas> ARP -A

Interface : 10.33.16.198 --- 0x8
  Adresse Internet      Adresse physique      Type
  10.33.19.254          00-c0-e7-e0-04-4e     dynamique
  10.33.19.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```

### Trouvez comment afficher les informations sur une carte IP (change selon l'OS)
![](https://i.imgur.com/cbZFqcF.png)

## 2. Modifications des informations

### A. Modification d'adresse IP (part 1)

-Allons donc sur les connexions réseau du panneau de configuration 

![](https://i.imgur.com/exZZsmw.png)

-Allons dans propriétés et changeons l'adresse IP 
![](https://i.imgur.com/4EEbVhU.png)

-Nous avons un message d'erreur 
![](https://i.imgur.com/uueph4z.png)

Le message d'erreur s'affiche car nous ne sommes pas prioritaire sur l'adresse IP configurée. 

# II. Exploration locale en duo

### Modifiez l'IP des deux machines pour qu'elles soient dans le même réseau
![](https://i.imgur.com/z3FqGi9.png)

### Vérifier à l'aide d'une commande que votre IP a bien été changée :

```
PS C:\Users\lucas>ipconfig /all
  
   
   Carte Ethernet Ethernet :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Realtek Gaming GbE Family Controller
   Adresse physique . . . . . . . . . . . : C0-18-03-59-30-32
   DHCP activé. . . . . . . . . . . . . . : Non
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::e1c2:fd45:f4f0:e17d%18(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.251(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Passerelle par défaut. . . . . . . . . :
   IAID DHCPv6 . . . . . . . . . . . : 163584003
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-29-6A-1A-C4-C0-18-03-59-30-32
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
```
### Vérifier que les deux machines se joignent

```
PS C:\Users\lucas> ping  10.10.10.250

Envoi d’une requête 'Ping'  10.10.10.250 avec 32 octets de données :
Réponse de 10.10.10.250 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.250 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.250 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.250 : octets=32 temps=2 ms TTL=128

Statistiques Ping pour 10.10.10.250:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 2ms, Moyenne = 1ms
```
### Déterminer l'adresse MAC de votre correspondant

```
arp -a
Interface : 10.10.10.251 --- 0x12
  Adresse Internet      Adresse physique      Type
  10.10.10.250          b0-25-aa-47-c7-a4     dynamique
  10.10.10.255          ff-ff-ff-ff-ff-ff     statique
  169.254.139.228       b0-25-aa-47-c7-a4     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique![](https://i.imgur.com/vcmGB1k.png)

```
## Utilisation d'un des deux comme gateway

![](https://i.imgur.com/6GdBA4S.png)


### Tester l'accès internet :
```
PS C:\Users\lucas> ping 1.1.1.1

Envoi d’une requête 'Ping'  1.1.1.1 avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=20 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=20 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=21 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=20 ms TTL=55

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 20ms, Maximum
```

### Prouver que la connexion Internet passe bien par l'autre PC :
 
```
 PS C:\Users\lucas> tracert 192.168.137.2

Détermination de l’itinéraire vers DESKTOP-0JKFI2T [192.168.137.2]
avec un maximum de 30 sauts :

  1     2 ms     1 ms     1 ms  DESKTOP-0JKFI2T [192.168.137.2]

Itinéraire déterminé.
```
## Petit chat privé

### sur le PC serveur : 
```
PS C:\Users\lucas\netcat-1.11> .\nc.exe -l -p 8888
```
### sur le PC client: 
```
PS C:\Users\lucas\Desktop\netcat-win32-1.11\netcat-1.11> .\nc.exe 192.168.137.1 8888
```
### CONVERSATION : 
```
[fmaxance] : OUI !
[blucas] : OUI

```
### Visualiser la connexion en cours

```
PS C:\Users\lucas> netstat -a -n -b

  TCP    192.168.137.1:8888     192.168.137.2:56320    ESTABLISHED
 [nc.exe]
```
### Pour aller un peu plus loin

IP non définie : 
```
PS C:\Users\lucas\netcat-1.11> ./nc.exe -l -p 8888

PS C:\Users\lucas> netstat -a -n -b | Select-String 8888

  TCP    0.0.0.0:8888           0.0.0.0:0              LISTENING
```
N'importe qui peut se connecter sur le serveur car l'IP est non définie.


IP définie : 
```
PS C:\Users\lucas\netcat-1.11> ./nc.exe -l -p 8888 -s 192.168.137.1
PS C:\Users\lucas> netstat -a -n -b | Select-String 8888

  TCP    192.168.137.1:8888     0.0.0.0:0              LISTENING
```
## Firewall
### Activez et configurez votre firewall

![](https://i.imgur.com/rKmcEgB.png)


# III. Manipulations d'autres outils/protocoles côté client

## 1. DHCP 


```
PS C:\Users\lucas> ipconfig /all
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Intel(R) Wi-Fi 6 AX201 160MHz
   Adresse physique . . . . . . . . . . . : C4-BD-E5-A7-5B-3E
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::c5ba:3ca0:a6eb:1119%9(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.17.12(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Bail obtenu. . . . . . . . . . . . . . : jeudi 6 octobre 2022 09:01:40
   Bail expirant. . . . . . . . . . . . . : vendredi 7 octobre 2022 09:01:35
   Passerelle par défaut. . . . . . . . . : 10.33.19.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.19.254
   IAID DHCPv6 . . . . . . . . . . . : 80002533
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2A-BC-B9-32-C4-BD-E5-A7-5B-3E
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       8.8.4.4
                                       1.1.1.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
```
## 2. DNS 

### ** Trouver l'adresse IP du serveur DNS que connaît votre ordinateur**
```
PS C:\Users\lucas> ipconfig /all

Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
```

### Utiliser, en ligne de commande l'outil nslookup (Windows, MacOS) ou dig (GNU/Linux, MacOS) pour faire des requêtes DNS à la main

```
PS C:\Users\lucas> nslookup google.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:80f::200e
          216.58.214.174
```

```
PS C:\Users\lucas> nslookup ynov.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:ae9
          2606:4700:20::ac43:4ae2
          2606:4700:20::681a:be9
          172.67.74.226
          104.26.11.233
          104.26.10.233
```
si plusieur adresse IP = repartition de charge pour gérer le flux de clients (google à une autre façon de gérer les flux de clients)
```
PS C:\Users\lucas> nslookup 231.34.113.12
Serveur :   dns.google
Address:  8.8.8.8

*** dns.google ne parvient pas à trouver 231.34.113.12 : Non-existent domain
```
adresse IP non utiliser
```
PS C:\Users\lucas> nslookup 78.34.2.17
Serveur :   dns.google
Address:  8.8.8.8

Nom :    cable-78-34-2-17.nc.de
Address:  78.34.2.17
```
adresse IP associer au nom 'cable-78-34-2-17.nc.de'

## IV. Wireshark
### Utilisez le pour observer les trames qui circulent entre vos deux carte Ethernet. Mettez en évidence : 
![](https://i.imgur.com/9Eef9Jf.png)

![](https://i.imgur.com/NCGT1TW.png)

![](https://i.imgur.com/9v8O7xz.png)

