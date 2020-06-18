# TP 4 - Cisco, Routage, DHCP
## I. Topologie 1 : simple
### 2. Mise en place
#### B. Définition d'IPs statiques


**Admin1**
Le selLinux est désactivé.
```
> Current mode: permissive
```

Ip static défini sur 10.4.1.11.
```
> enp0s3: inet 10.4.1.11/24 brd 10.4.1.255
```

Hostname défini sur admin1.b1.tp4.

---

**Router1**
```
>show ip int br
>Interface                  IP-Address      OK? Method >Status                Protocol
>FastEthernet0/0            10.4.1.254      YES manual >up                    up
>FastEthernet1/0            10.4.2.254      YES manual >up                    up
```

Hostname défini sur router1.b1.tp4

---

**Guest1**
```
>ip 10.4.2.11 255.255.255.0
>Checking for duplicate address...
>PC1 : 10.4.2.11 255.255.255.0
```

Hostname défini sur router1.b1.tp4

---

**Vérification :**
>**Guest1 ping Router1**
```
>ping 10.4.2.254
>84 bytes from 10.4.2.254 icmp_seq=1 ttl=255 time=10.000 ms
>84 bytes from 10.4.2.254 icmp_seq=2 ttl=255 time=6.068 ms
>84 bytes from 10.4.2.254 icmp_seq=3 ttl=255 time=10.052 ms
>84 bytes from 10.4.2.254 icmp_seq=4 ttl=255 time=3.209 ms
>84 bytes from 10.4.2.254 icmp_seq=5 ttl=255 time=6.198 ms
```
---
>**Admin1 ping Router1**
![](https://i.imgur.com/gwmyVWc.png)
---
>**Router1 ping Admin1**
```
>router1.b1.tp4#ping 10.4.1.11
>Type escape sequence to abort.
>Sending 5, 100-byte ICMP Echos to 10.4.1.11, timeout is 2 seconds:
>Success rate is 100 percent (5/5), round-trip min/avg/max = 60/68/84 ms
```
---
>**Router1 ping Guest1**
```
>router1.b1.tp4#ping 10.4.2.11
>Type escape sequence to abort.
>Sending 5, 100-byte ICMP Echos to 10.4.2.11, timeout is 2 seconds:
>Success rate is 100 percent (5/5), round-trip min/avg/max = 56/64/68 ms
```
---
>**Table ARP Router1**
```
>Protocol  Address          Age (min)  Hardware Addr   Type   Interface
>Internet  10.4.1.11               2   0800.277a.9517  ARPA   FastEthernet0/0
>Internet  10.4.2.11               7   0050.7966.6800  ARPA   FastEthernet1/0
>Internet  10.4.1.254              -   cc01.90f4.0000  ARPA   FastEthernet0/0
>Internet  10.4.2.254              -   cc01.90f4.0010  ARPA   FastEthernet1/0
```
---
>**Adresse Mac**
>- **Guest1**
```
>cc:01:90:f4:00:10
```

>- **Admin1**
```
>08:00:27:7a:95:17
```

---

#### C. Routage

**Admin1**
```
>- Ajout de 10.4.2.0/24 via 10.4.1.254 dev enp0s3 dans /etc/sysconfig/network-scripts/route-enp0s3
>- Redémarrer le réseaux avec sudo systemctl restart network
```
---
**Guest1**
```
>PC1 : 10.4.2.11 255.255.255.0 gateway 10.4.2.254
```
---
**Vérification**
>**Admin1 ping Guest1**
>![](https://i.imgur.com/HDiUd0o.png)
---
>**Guest1 ping Admin1**
```
>ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=19.813 ms
>84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=19.440 ms
>84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=21.798 ms
>84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=20.619 ms
>84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=17.430 ms
```
---
>**Route Admin1 > Guest1**
>![](https://i.imgur.com/4ZM4Wm9.png)
---
>**Route Guest1 > Admin1**
```
>trace 10.4.1.11
>trace to 10.4.1.11, 8 hops max, press Ctrl+C to stop
> 1   10.4.2.254   5.746 ms  9.001 ms  8.585 ms
> 2   *10.4.1.11   21.805 ms (ICMP type:3, code:10, Host administratively prohibited)
```

## II. Topologie 2 : dumb switches
### 2. Mise en place
#### A. Topologie GNS3

![](https://i.imgur.com/DEJrKVN.png)

#### C. Vérification

**Vérification**
>**Admin1 ping Guest1**
>![](https://i.imgur.com/J3kzBTB.png)
---
>**Guest1 ping Admin1**
```
>ping 10.4.1.11
>84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=19.772 ms
>84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=17.889 ms
>84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=14.999 ms
>84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=15.976 ms
>84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=16.874 ms
```
---
>**Route Admin1 > Guest1**
>![](https://i.imgur.com/DorR4bl.png)
---
>**Route Guest1 > Admin1**
```
>trace 10.4.1.11
>trace to 10.4.1.11, 8 hops max, press Ctrl+C to stop
> 1   10.4.2.254   3.860 ms  10.007 ms  8.642 ms
> 2   *10.4.1.11   20.747 ms (ICMP type:3, code:10, Host administratively prohibited)
```

## III. Topologie 3 : adding nodes and NAT
### 2. Mise en place
#### B. VPCS

On configure tout les VPCS comme le Guest1 avec les bonnes ips.

**Guest2**
```
>ip 10.4.2.12 255.255.255.0 10.4.2.254
```
---
**Guest3**
```
>ip 10.4.2.13 255.255.255.0 10.4.2.254
```
---
**Vérification**
>- **Guest2 ping Admin1**
```
>ping 10.4.1.11
>84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=15.354 ms
>84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=13.960 ms
>84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=17.909 ms
>84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=19.874 ms
>84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=21.853 ms
```

>- **Guest3 ping Admin1**
```
>ping 10.4.1.11
>84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=19.868 ms
>84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=18.987 ms
>84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=12.830 ms
>84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=19.639 ms
>84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=13.608 ms
```

#### C. Accès WAN

>- **Routeur**
>-- **IP en DHCP** :
```
>Interface                  IP-Address      OK? Method Status                Protocol
>FastEthernet0/0            10.4.1.254      YES NVRAM  up                    up
>FastEthernet1/0            10.4.2.254      YES NVRAM  up                    up
>FastEthernet2/0            unassigned      YES DHCP   up                    up
```
>-- **Nat simple** :
```
>Interface                  IP-Address      OK? Method Status                Protocol
>FastEthernet0/0            10.4.1.254      YES NVRAM  up                    up
>FastEthernet1/0            10.4.2.254      YES NVRAM  up                    up
>FastEthernet2/0            192.168.122.24  YES DHCP   up                    up
>NVI0                       unassigned      NO  unset  up                    up
```
---
>- **Clients**
>-- **Route**
>Les routes des clients et de l'admin sont déjà définie.
>-- **Utilisation serveur DNS**
>**Admin1** : ![](https://i.imgur.com/I8AK4RN.png)
>**Guests** : ip dns 1.1.1.1
---
**Vérification**
>**Ping google.com Routeur1**
```
>ping 172.217.19.228
>Type escape sequence to abort.
>Sending 5, 100-byte ICMP Echos to 172.217.19.228, timeout is 2 seconds:
>Success rate is 100 percent (5/5), round-trip min/avg/max = 36/52/72 ms
```
---
>**Ping google.com Admin1**
```
>Bug GNS3 Passage au TP5
```
---
>**Ping google.com Guest1**
```
>ping www.google.com
>www.google.com resolved to 172.217.19.228
>84 bytes from 172.217.19.228 icmp_seq=1 ttl=50 time=702.920 ms
>84 bytes from 172.217.19.228 icmp_seq=2 ttl=50 time=342.512 ms
>84 bytes from 172.217.19.228 icmp_seq=3 ttl=50 time=69.188 ms
>84 bytes from 172.217.19.228 icmp_seq=4 ttl=50 time=77.212 ms
>84 bytes from 172.217.19.228 icmp_seq=5 ttl=50 time=46.509 ms
```
---
>**Ping google.com Guest2**
```
>ping www.google.com
>www.google.com resolved to 216.58.213.132
>84 bytes from 216.58.213.132 icmp_seq=1 ttl=50 time=52.897 ms
>84 bytes from 216.58.213.132 icmp_seq=2 ttl=50 time=46.340 ms
>84 bytes from 216.58.213.132 icmp_seq=3 ttl=50 time=47.492 ms
>84 bytes from 216.58.213.132 icmp_seq=4 ttl=50 time=57.070 ms
>84 bytes from 216.58.213.132 icmp_seq=5 ttl=50 time=50.288 ms
```
---
>**Ping google.com Guest3**
```
>ping www.google.com
>www.google.com resolved to 216.58.209.228
>84 bytes from 216.58.209.228 icmp_seq=1 ttl=50 time=71.241 ms
>84 bytes from 216.58.209.228 icmp_seq=2 ttl=50 time=66.275 ms
>84 bytes from 216.58.209.228 icmp_seq=3 ttl=50 time=71.563 ms
>84 bytes from 216.58.209.228 icmp_seq=4 ttl=50 time=61.872 ms
>84 bytes from 216.58.209.228 icmp_seq=5 ttl=50 time=53.137 ms
```
