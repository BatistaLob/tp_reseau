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
[capture discord](./discord.pcap)

- Pour FallGuys

```
PS C:\Users\lucas> netstat -n -a -p udp

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  UDP    0.0.0.0:58346          188.42.41.6:7877         
```

[capture FallGuys](./FallGuys.pcap)


   - Pour Instagram

```
PS C:\Users\lucas> netstat -n -a -p udp

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  UDP    0.0.0.0:50152          81.65.32.162:443     
[msedge.exe]
```

[capture instagram](./instagrma.pcap)


- Pour Zoom

```
PS C:\Users\lucas> netstat -n -a -p udp

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  UDP    0.0.0.0:60727          206.247.74.210:8801
  [Zoom.exe]
```

[capture Zoom](./Zoom.pcap)


- PourEdge 

```
PS C:\Users\lucas> netstat -b -n

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  TCP    10.33.17.12:63046      99.83.179.177:443      ESTABLISHED
 [msedge.exe]
```
[capture edge](./Edge.pcap)

## II. Mise en place

### 1. SSH

###  Examinez le trafic dans Wireshark

[capture ssh](./ssh.pcap)

### Demandez aux OS 
```
lucas@LAPTOP-GEJ2DKFJ MINGW64 ~
$ netstat -b -n

Connexions actives

  Proto  Adresse locale         Adresse distante       ▒tat
  TCP    10.4.1.1:56346         10.4.1.11:22           ESTABLISHED
 [ssh.exe]
```
