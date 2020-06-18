# BERGEZCASALOU TP3
## Compte rendu TP4

### I. Topologie 1 : simple

#### 2. Mise en place
##### B. Définition d'IPs statiques

- admin1


![](https://i.imgur.com/LNd4OMN.png)


```bash
[user@admin1 ~]$ hostname
admin1
```


- router

```bash
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.4.1.254      YES manual up                    up  
FastEthernet1/0            10.4.2.254      YES manual up                    up  
R1#copy running-config startup-config
Destination filename [startup-config]?
Building configuration...
[OK]
```

- guest1

```bash
VPCS> ip 10.4.2.11 /24 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.11 255.255.255.0 gateway 10.4.2.254
```

- vérification par ping

```bash
guest1> ping 10.4.2.254
84 bytes from 10.4.2.254 icmp_seq=1 ttl=255 time=10.274 ms
84 bytes from 10.4.2.254 icmp_seq=2 ttl=255 time=10.717 ms
84 bytes from 10.4.2.254 icmp_seq=3 ttl=255 time=9.850 ms
84 bytes from 10.4.2.254 icmp_seq=4 ttl=255 time=4.953 ms
84 bytes from 10.4.2.254 icmp_seq=5 ttl=255 time=7.134 ms
^C
```

```bash
R1#ping 10.4.2.11

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.4.2.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/33/36 ms
```

```bash
![](https://i.imgur.com/qkYPtib.png)
```

```bash
router1#ping 10.4.2.11

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.4.2.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/34/36 ms
```
 - on compare les MAC
```bash
router1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.4.1.11               8   0800.2753.e008  ARPA   FastEthernet0/0
Internet  10.4.2.11              17   0050.7966.6800  ARPA   FastEthernet1/0
Internet  10.4.1.254              -   cc01.043b.0000  ARPA   FastEthernet0/0
Internet  10.4.2.254              -   cc01.043b.0010  ARPA   FastEthernet1/0
```

##### B. Routage

je modifie le fichier “/etc/sysconfig/network-scripts/route-enp0s8” et entre "10.4.2.0 via 10.4.1.254 dev enp0s3"

```bash
PC1> ip 10.4.2.11 /24 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.11 255.255.255.0 gateway 10.4.2.254
```

ping de guest1 vers admin1
```bash
PC1> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=14.297 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=16.493 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=20.545 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=11.921 ms
^C
```
ping de admin1 vers guest1
![](https://i.imgur.com/fyRc3vg.png)

traceroute
![](https://i.imgur.com/4I9DcoA.png)

### II. Topologie 2: dumb switches
#### C. Vérification

![](https://i.imgur.com/qTUPD6t.png)


ping de admin1 vers guest1
![](https://i.imgur.com/yMdlJ3w.png)

ping de guest1 vers admin1
```bash
PC1> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=19.485 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=13.787 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=15.856 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=17.851 ms
^C
```

traceroute
![](https://i.imgur.com/pF5ZSW9.png)


### III. Topologie 3 : adding nodes and NAT 
#### B. VPCS

attribution d'ip et de passerelles à guest2 et 3
```bash
guest2> ip 10.4.2.12 /24 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.12 255.255.255.0 gateway 10.4.2.254
```

```bash
guest3> ip 10.4.2.13 /24 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.13 255.255.255.0 gateway 10.4.2.254
```

les vpcs peuvent joindre admin1
```bash
guest2> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=21.492 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=20.624 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=20.540 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=19.492 ms
^C
```

```bash
guest3> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=19.676 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=12.891 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=17.947 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=20.859 ms
^C
```

#### C. Accès WAN
-  récupérer une IP en DHCP sur l'interface branchée au nuage NAT
```bash
router1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.4.1.254      YES NVRAM  up                    up  
FastEthernet1/0            10.4.2.254      YES NVRAM  up                    up  
FastEthernet2/0            192.168.56.102  YES DHCP   up                    up  
```

- configurer un NAT simple sur le routeur
```bash
router1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.4.1.254      YES NVRAM  up                    up  
FastEthernet1/0            10.4.2.254      YES NVRAM  up                    up  
FastEthernet2/0            192.168.56.102  YES DHCP   up                    up  
NVI0                       unassigned      NO  unset  up                    up  
```

> Configurer les clients

admin1
![](https://i.imgur.com/mGgwp80.png)

guest1, 2, 3
```bash
ip dns 1.1.1.1
```

```bash
router1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.4.1.254      YES NVRAM  up                    up  
FastEthernet1/0            10.4.2.254      YES NVRAM  up                    up  
FastEthernet2/0            192.168.56.102  YES DHCP   up                    up  
NVI0                       unassigned      NO  unset  up                    up  
```

/!\toujours pas acces au wan

```bash
guest1> ping www.google.com
Cannot resolve www.google.com

guest1> ping 8.8.8.8
*10.4.2.254 icmp_seq=1 ttl=255 time=10.516 ms (ICMP type:3, code:1, Destination host unreachable)
*10.4.2.254 icmp_seq=2 ttl=255 time=5.365 ms (ICMP type:3, code:1, Destination host unreachable)
*10.4.2.254 icmp_seq=3 ttl=255 time=10.260 ms (ICMP type:3, code:1, Destination host unreachable)
*10.4.2.254 icmp_seq=4 ttl=255 time=3.272 ms (ICMP type:3, code:1, Destination host unreachable)
*10.4.2.254 icmp_seq=5 ttl=255 time=10.043 ms (ICMP type:3, code:1, Destination host unreachable)
```

### IV. Topologie 4 : home-made DHCP

setup de la vm fait 