# TP 3 - Routage, ARP, Spéléologie réseau

## Préparation de l'environnement

### 2. Mise en place du lab

#### Preuve chacun des points de la préparation de l'environnement ci-dessus ont été respecté : 

-Carte NAT désactivées : 

Pour le "client" : 

```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:a5:01:99 brd ff:ff:ff:ff:ff:ff
```

Pour le "server" : 

```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:ee:13:89 brd ff:ff:ff:ff:ff:ff
```

Pour le "router" : 

```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:df:5c:29 brd ff:ff:ff:ff:ff:ff
```

-Serveur SSH fonctionnel qui écoute sur le port 7777/tcp :

Pour le "client" :

```
[chris@client1 ~]$ sudo ss -ltunp
Netid  State      Recv-Q Send-Q            Local Address:Port                           Peer Address:Port
tcp    LISTEN     0      100                   127.0.0.1:25                                        *:*                   users:(("master",pid=1263,fd=13))
tcp    LISTEN     0      128                           *:7777                                      *:*                   users:(("sshd",pid=3785,fd=3))
tcp    LISTEN     0      100                       [::1]:25                                     [::]:*                   users:(("master",pid=1263,fd=14))
tcp    LISTEN     0      128                        [::]:7777                                   [::]:*                   users:(("sshd",pid=3785,fd=4))
```

Pour le "server" : 

```
[chris@server1 ~]$ sudo ss -ltunp
Netid  State      Recv-Q Send-Q            Local Address:Port                           Peer Address:Port
tcp    LISTEN     0      100                   127.0.0.1:25                                        *:*                   users:(("master",pid=1260,fd=13))
tcp    LISTEN     0      128                           *:7777                                      *:*                   users:(("sshd",pid=1028,fd=3))
tcp    LISTEN     0      100                       [::1]:25                                     [::]:*                   users:(("master",pid=1260,fd=14))
tcp    LISTEN     0      128                        [::]:7777                                   [::]:*                   users:(("sshd",pid=1028,fd=4))
```

Pour le "router" : 

```
[chris@router ~]$ sudo ss -ltunp
Netid  State      Recv-Q Send-Q            Local Address:Port                           Peer Address:Port
tcp    LISTEN     0      100                   127.0.0.1:25                                        *:*                   users:(("master",pid=1380,fd=13))
tcp    LISTEN     0      128                           *:7777                                      *:*                   users:(("sshd",pid=1694,fd=3))
tcp    LISTEN     0      100                       [::1]:25                                     [::]:*                   users:(("master",pid=1380,fd=14))
tcp    LISTEN     0      128                        [::]:7777                                   [::]:*                   users:(("sshd",pid=1694,fd=4))
```

-Pare-feu activé et configuré :

Pour le "client" :

```
[chris@client1 ~]$ sudo firewall-cmd --list-all
[sudo] password for chris:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client ssh
  >ports: 7777/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

```

Pour le "server" : 

```
[chris@server1 ~]$ sudo firewall-cmd --list-all
[sudo] password for chris:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client ssh
  ports: 7777/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

Pour le "router" : 

```
[chris@router ~]$ sudo firewall-cmd --list-all
[sudo] password for chris:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8 enp0s9
  sources:
  services: dhcpv6-client ssh
 >ports: 7777/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

-Nom configuré :

Pour le "client" :

```
[chris@client1 ~]$ hostname --fqdn
client1.net1.tp3
```

Pour le "server" : 

```
[chris@server1 ~]$  hostname --fqdn
server1.net2.tp3
```

Pour le "router" : 

```
[chris@router ~]$  hostname --fqdn
router.tp3
```

-Fichiers /etc/hosts de toutes les machines configurés :

Pour le "client" :

```
[chris@client1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11   client1.net1.tp3
10.3.2.11   server1.net2.tp3
10.3.1.254  router.tp3
10.3.2.254  router.tp3
```

Pour le "server" : 

```
[chris@server1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11   client1.net1.tp3
10.3.2.11   server1.net2.tp3
10.3.1.254  router.tp3
10.3.2.254  router.tp3
```

Pour le "router" : 

```
[chris@router ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11   client1.net1.tp3
10.3.2.11   server1.net2.tp3
10.3.1.254  router.tp3
10.3.2.254  router.tp3
```

-Réseaux et adressage des machines :

Pour le "client" : 

>ip a
```
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:ba:6d:8d brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.11/24 brd 10.3.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feba:6d8d/64 scope link
       valid_lft forever preferred_lft forever
```

Pour le "server" : 

>ip a
```
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:f1:d0:0c brd ff:ff:ff:ff:ff:ff
    inet 10.3.2.11/24 brd 10.3.2.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fef1:d00c/64 scope link
       valid_lft forever preferred_lft forever
```

Pour le "router" : 

>ip a
```
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:fe:64:94 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.254/24 brd 10.3.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fefe:6494/64 scope link
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:3d:78:82 brd ff:ff:ff:ff:ff:ff
    inet 10.3.2.254/24 brd 10.3.2.255 scope global noprefixroute enp0s9
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe3d:7882/64 scope link
       valid_lft forever preferred_lft forever
```

>Ping "client" -> "router"

```
[chris@client1 ~]$ ping router.tp3
PING router.tp3 (10.3.1.254) 56(84) bytes of data.
64 bytes from router.tp3 (10.3.1.254): icmp_seq=1 ttl=64 time=0.435 ms
64 bytes from router.tp3 (10.3.1.254): icmp_seq=2 ttl=64 time=0.923 ms
64 bytes from router.tp3 (10.3.1.254): icmp_seq=3 ttl=64 time=0.662 ms
64 bytes from router.tp3 (10.3.1.254): icmp_seq=4 ttl=64 time=0.888 ms
^C
--- router.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3014ms
rtt min/avg/max/mdev = 0.435/0.727/0.923/0.196 ms
```

>Ping "router" -> "client" 

```
[chris@router ~]$ ping client1.net1.tp3
PING client1.net1.tp3 (10.3.1.11) 56(84) bytes of data.
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=1 ttl=64 time=0.393 ms
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=2 ttl=64 time=0.928 ms
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=3 ttl=64 time=0.901 ms
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=4 ttl=64 time=0.931 ms
^C
--- client1.net1.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
rtt min/avg/max/mdev = 0.393/0.788/0.931/0.229 ms
```

>Ping "server" -> "router"

```
[chris@server1 ~]$ ping router.tp3
PING router.tp3 (10.3.2.254) 56(84) bytes of data.
64 bytes from router.tp3 (10.3.2.254): icmp_seq=1 ttl=64 time=0.738 ms
64 bytes from router.tp3 (10.3.2.254): icmp_seq=2 ttl=64 time=0.885 ms
64 bytes from router.tp3 (10.3.2.254): icmp_seq=3 ttl=64 time=0.872 ms
64 bytes from router.tp3 (10.3.2.254): icmp_seq=4 ttl=64 time=0.929 ms
^X^C
--- router.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3010ms
rtt min/avg/max/mdev = 0.738/0.856/0.929/0.071 ms
```

>Ping "router" -> "server"

```
[chris@router ~]$ ping server1.net2.tp3
PING server1.net2.tp3 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=1 ttl=64 time=0.524 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=2 ttl=64 time=0.337 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=3 ttl=64 time=0.906 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=4 ttl=64 time=0.350 ms
^C
--- server1.net2.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3019ms
rtt min/avg/max/mdev = 0.337/0.529/0.906/0.230 ms
```

## I. Mise en place du routage

### 1. Configuration du routage sur router

On cherche à faire en sorte que la vm router puisse servir de passerelle pour d'autre réseau. Pour cela, on rentre la commande : 

>sudo sysctl -w net.ipv4.conf.all.forwarding=1 

```
[chris@router ~]$ echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
net.ipv4.ip_forward = 1
```

La sortie de cette commande nous confirme bien que les changements ont été bien éffectués.

>sudo sysctl net.ipv4.conf.all.forwarding

De plus, grâce à cette commande, on peut vérifier que les changements ont été éffectué.

```
[chris@router ~]$ sudo sysctl net.ipv4.conf.all.forwarding
net.ipv4.conf.all.forwarding = 1
```

### 2. Ajouter les routes statiques

On veut ajouter les routes statiques pour que server puisse communiquer avec client et vice versa.
Pour cela, sur "client", on va rentrer une premiere route statique avec la commande "ip route add" : 

>[chris@client1 ~]$ sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s8

Cette commande va rentrer de facon temporaire la route entre le reseau du "client" et le reseau "server". Si on veut rentrer de facon definitive la cette route, il faut alors écrire dans le fichier *"/etc/sysconfig/network-scripts/route-enp0s8"* et entrer dedans *"10.3.2.0/24 via 10.3.1.254 dev enp0s8"*

Ensuite, on va faire la même chose sur "serveur". On entre une route statique de facon temporaire : 

>[chris@server1 ~]$ sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s8

De même, cette commande va rentrer de facon temporaire la route entre le reseau du "client" et le reseau "server". Si on veut rentrer de facon definitive la cette route, il faut alors écrire dans le fichier *"/etc/sysconfig/network-scripts/route-enp0s8"* et entrer dedans *"10.3.1.0/24 via 10.3.2.254 dev enp0s8"*

Maintenant, il faut vérifier que les routes statiques mises en place sont bien prises en compte. 
D'abord, on verifie sur "client" : 

```
[chris@server1 ~]$ ip r s
default via 10.3.1.254 dev enp0s8 proto static metric 100
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 100
10.3.2.0/24 via 10.3.1.254 dev enp0s8
10.3.2.0/24 via 10.3.1.254 dev enp0s8 proto static metric 100
```

On voit qu'avec la ligne *"10.3.2.0/24 via 10.3.1.254 dev enp0s8"*, le changement a bien été pris en compte.


Puis, on fait la même chose sur "server" (que ce soit de facon temporaire ou bien définitive) : 

```
[chris@server1 ~]$ ip r s
default via 10.3.2.254 dev enp0s8 proto static metric 100
10.3.1.0/24 via 10.3.2.254 dev enp0s8
10.3.1.0/24 via 10.3.2.254 dev enp0s8 proto static metric 100
10.3.2.0/24 dev enp0s8 proto kernel scope link src 10.3.2.11 metric 100
```

On voit qu'avec la ligne *"10.3.1.0/24 via 10.3.2.254 dev enp0s8"*, le changement a bien été pris en compte.

Ensuite, il faut verifier que la route statique entre "client" et "server" soit bien effective. Pour cela, on va faire un ping.
D'abord, on va ping "server" depuis "client" : 

```
[chris@client1 ~]$ ping server1.net2.tp3
PING server1.net2.tp3 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=1 ttl=63 time=0.911 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=2 ttl=63 time=1.72 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=3 ttl=63 time=1.62 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=4 ttl=63 time=1.65 ms
^C
--- server1.net2.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3018ms
rtt min/avg/max/mdev = 0.911/1.480/1.728/0.330 ms
```

On voit bien que la route statique depuis "client" vers "server" est fonctionnel car le ping est bien envoyé et recu par "server".

Puis, on va ping "client" depuis "server" : 

```
[chris@server1 ~]$ ping client1.net1.tp3
PING client1.net1.tp3 (10.3.1.11) 56(84) bytes of data.
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=1 ttl=63 time=0.658 ms
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=2 ttl=63 time=1.56 ms
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=3 ttl=63 time=1.69 ms
64 bytes from client1.net1.tp3 (10.3.1.11): icmp_seq=4 ttl=63 time=1.70 ms
^C
--- client1.net1.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3017ms
rtt min/avg/max/mdev = 0.658/1.404/1.705/0.437 ms
```

Ici aussi, la route statique depuis "server" vers "client" est fonctionnel car le ping est bien envoyé et recu par "client".

Pour finir dans cette partie, il faut vérifier que le passage par "router" s'effectue bien. Pour cela, on va rentrer la commande "traceroute" sur les vms "client" et "server".
On vérifie d'abord sur "client" : 

```
[chris@client1 ~]$ traceroute server1.net2.tp3
traceroute to server1.net2.tp3 (10.3.2.11), 30 hops max, 60 byte packets
 1  router.tp3 (10.3.1.254)  0.462 ms  0.330 ms  0.246 ms
 2  router.tp3 (10.3.1.254)  0.273 ms !X  0.270 ms !X  0.264 ms !X
```

On voit bien que le message passe par "router".

On vérifie aussi sur "server" : 

```
[chris@server1 ~]$ traceroute client1.net1.tp3
traceroute to client1.net1.tp3 (10.3.1.11), 30 hops max, 60 byte packets
 1  router.tp3 (10.3.2.254)  0.324 ms  0.219 ms  0.141 ms
 2  router.tp3 (10.3.2.254)  0.093 ms !X  0.175 ms !X  0.152 ms !X
```

### 3. Comprendre le routage


On cherche à comprendre le routage. Pour cela, on va lancer "tcpdump" depuis le routeur sur le client grâce a l'interface réseau "enp0s8". Une fois lancé sur le "router", on va lancer un ping depuis "client" vers "server".
Pour lancer "tcpdump", il faut utiliser cette commande : 

>sudo tcpdump -i enp0s8 -w capture1.pcap not port 7777

Puis on lance le ping et on regarde le resultat obtenu sur tcpdump sur la vm "router" : 

```
[chris@router ~]$ sudo tcpdump -i enp0s8 -w capture1.pcap not port 7777
[sudo] password for chris:
tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
^C6 packets captured
6 packets received by filter
0 packets dropped by kernel
```

On peut voir que tcpdump a capturé 6 packets entre le ping de "client" vers "server".

On regarde ensuite ce qui se passe dans le wireshark : 

Wireshark du ping envoyé depuis client : 

![](https://i.imgur.com/sXmbHTp.png)

![](https://i.imgur.com/BowGfxr.png)

Wireshark du ping envoyé depuis server : 

![](https://i.imgur.com/w7lhGK8.png)

![](https://i.imgur.com/ru3LSCa.png)

Grâce au wireshark, on peut voir des informations sur le message envoyé depuis clieent vers server. On peut obtenir les IPS sources et de destination, ainsi que les MAC sources et de destination. Ce sont pour l'instant, les seuls informations dont on a besoin.

Donc, on peut dresser un tableau, pour representer ce qui ce passe quand on ping "server" depuis "client" : 

|             | MAC src       | MAC dst       | IP src       | IP dst       |
| ----------- | ------------- | ------------- | ------------ | ------------ |
| Dans `net1` (trame qui entre dans `router`) |0a:00:27:00:00:12  | 08:00:27:fe:64:94 | 10.3.1.11 | 10.3.2.11 |
| Dans `net2` (trame qui sort de `router`) | 08:00:27:3d:78:82 | 0a:00:27:00:00:16 | 10.3.2.254 | 10.3.2.11 |


## II. ARP

### 1. Tables ARP

On veut afficher la table ARP de chacune des machines. Pour cela, il faut d'abord ping "server", depuis "client" : 

```
[chris@client1 ~]$ ping server1.net2.tp3
PING server1.net2.tp3 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=1 ttl=63 time=1.25 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=2 ttl=63 time=1.59 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=3 ttl=63 time=1.14 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=4 ttl=63 time=1.79 ms
^X64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=5 ttl=63 time=1.60 ms
^C
--- server1.net2.tp3 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4015ms
rtt min/avg/max/mdev = 1.148/1.478/1.795/0.243 ms
```

Ensuite, pour afficher la table ARP, il faut entrer la commande : 

>ip neigh show

On le fait donc sur chacune des machines:

Sur "client" : 

```
[chris@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.1.254 dev enp0s8 lladdr 08:00:27:fe:64:94 REACHABLE
```

Le résultat de cette commande nous montre que "client" connaît l'adresse IP de la passerelle, c'est-à-dire l'adresse IP du "router".

Sur "server" : 

```
[chris@server1 ~]$ ip neigh show
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:16 REACHABLE
10.3.2.254 dev enp0s8 lladdr 08:00:27:3d:78:82 STALE
```

Le résultat de cette commande nous montre que "server" connaît l'adresse IP de la passerelle, c'est-à-dire l'adresse IP du "router".

Sur "router" : 

```
[chris@router ~]$ ip neigh show
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE
10.3.2.11 dev enp0s9 lladdr 08:00:27:f1:d0:0c STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 DELAY
10.3.1.11 dev enp0s8 lladdr 08:00:27:ba:6d:8d STALE
10.3.2.1 dev enp0s9 lladdr 0a:00:27:00:00:16 STALE
```

Le résultat de cette commande nous montre que "router" connaît les adresses IPS des vms "client", et "server"

### 2. Requêtes ARP

#### A. Table ARP 1

Pour vider la table ARP d'une vm, il faut entrer la commande : 
>sudo ip neigh flush all

Table ARP vide de "client" : 

```
[chris@client1 ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
```
Table ARP vide de "router" : 

```
[chris@router ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@router ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
```

Ping depuis "client" vers "server" : 

```
[chris@client1 ~]$ ping server1.net2.tp3
PING server1.net2.tp3 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=1 ttl=63 time=2.07 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=2 ttl=63 time=1.61 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=3 ttl=63 time=1.58 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=4 ttl=63 time=1.77 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=5 ttl=63 time=1.59 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=6 ttl=63 time=1.63 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=7 ttl=63 time=1.83 ms
^C
--- server1.net2.tp3 ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 6042ms
rtt min/avg/max/mdev = 1.584/1.730/2.075/0.173 ms
```

Table ARP après le ping : 

Sur le "client" : 

```
[chris@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.1.254 dev enp0s8 lladdr 08:00:27:fe:64:94 REACHABLE
```
On observe que l'entrée vers server1 est possible car on lit REACHABLE

Sur le "router" : 

```
[chris@router ~]$ ip neigh show
10.3.2.11 dev enp0s9 lladdr 08:00:27:f1:d0:0c REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.1.11 dev enp0s8 lladdr 08:00:27:ba:6d:8d REACHABLE
```

#### B. Table ARP 2

Table ARP vide de "client" : 

```
[chris@client1 ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
```

Table ARP vide de "server" : 

```
[chris@server1 ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@server1 ~]$ ip neigh show
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:16 REACHABLE
```

Table ARP vide de "router" : 

```
[chris@router ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@router ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
```

Ping depuis "client" vers "server" : 

```
[chris@client1 ~]$ ping server1.net2.tp3
PING server1.net2.tp3 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=1 ttl=63 time=1.87 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=2 ttl=63 time=1.84 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=3 ttl=63 time=1.60 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=4 ttl=63 time=1.60 ms
^C
--- server1.net2.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3015ms
rtt min/avg/max/mdev = 1.608/1.735/1.879/0.131 ms
```

Tables ARPS après le ping : 

sur "client" : 

```
[chris@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.1.254 dev enp0s8 lladdr 08:00:27:fe:64:94 REACHABLE
```

sur "server" : 

```
[chris@server1 ~]$ ip neigh show
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:16 REACHABLE
10.3.2.254 dev enp0s8 lladdr 08:00:27:3d:78:82 REACHABLE
```

La première ligne dit que l'entrée du router est valable. La deuxième ligne s'est ajouté après le ping. De plus, cette dit que l'entrée du client1 est possible.

sur "router" : 

```
[chris@router ~]$ ip neigh show
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE
10.3.2.11 dev enp0s9 lladdr 08:00:27:f1:d0:0c REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
10.3.1.11 dev enp0s8 lladdr 08:00:27:ba:6d:8d REACHABLE
```

#### C. tcpdump 1

Tables ARPS vides :

Sur client : 

```
[chris@router ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@router ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
[chris@router ~]$
```

Sur router : 

```
[chris@router ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@router ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
```

Ensuite, on lance tcpdump sur client dans un autre terminal : 

```
[chris@client1 ~]$ sudo tcpdump -i enp0s8 -w capture1.pcap not port 7777
[sudo] password for chris:
tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
```

Puis on ping server depuis client : 

```
[chris@client1 ~]$ ping server1.net2.tp3
PING server1.net2.tp3 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=1 ttl=63 time=1.25 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=2 ttl=63 time=2.34 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=3 ttl=63 time=0.840 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=4 ttl=63 time=0.575 ms
^C
--- server1.net2.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3013ms
rtt min/avg/max/mdev = 0.575/1.254/2.346/0.676 ms
```

Et on coupe le tcpdump : 

```
[chris@client1 ~]$ sudo tcpdump -i enp0s8 -w capture1.pcap not port 7777
[sudo] password for chris:
tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
^C12 packets captured
13 packets received by filter
0 packets dropped by kernel
```

#### D. tcpdump 2

Tables ARPS vides :

Sur client : 

```
[chris@client1 ~]$ sudo ip neigh flush all
[chris@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 REACHABLE
```

Sur server : 

```
[chris@server1 ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@server1 ~]$ ip neigh show
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:16 DELAY
```

Sur router : 

```
[chris@router ~]$ sudo ip neigh flush all
[sudo] password for chris:
[chris@router ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:12 DELAY
```

Ensuite, on lance tcpdump sur server : 

```
[chris@server1 ~]$ sudo tcpdump -i enp0s8 -w capture1.pcap not port 7777
[sudo] password for chris:
tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
```

De client, on va ping server : 

```
[chris@client1 ~]$ ping server1.net2.tp3
PING server1.net2.tp3 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=1 ttl=63 time=3.76 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=2 ttl=63 time=1.92 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=3 ttl=63 time=1.34 ms
64 bytes from server1.net2.tp3 (10.3.2.11): icmp_seq=4 ttl=63 time=1.68 ms
^C
--- server1.net2.tp3 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3018ms
rtt min/avg/max/mdev = 1.347/2.181/3.765/0.937 ms
```

Puis on coupe tcpdump sur server : 

```
[chris@server1 ~]$ sudo tcpdump -i enp0s8 -w capture1.pcap not port 7777
[sudo] password for chris:
tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
^C12 packets captured
12 packets received by filter
0 packets dropped by kernel
```




#### E. u okay bro ?

Lorsque que "client" envoie un ping vers "server", il se passe beaucoup de choses au niveau du router. Pour l'expliquer, il faut prendre un exemple simple, celui d'un bureau de poste.

Tout d'abord, on veut deposer à la poste une lettre (10.3.1.11). On va se diriger vers le bureau de poste (10.3.1.254). Lorsqu'on y sera, on va demander où on va déposer la lettre. Le postier va donc nous demander :

- D'ou provient la lettre, la source
- Où elle doit aller, la destination
- Quelle type d'envoie, le protocole

ceci correspond donc a l'adresse ip de où on envoie, l'adresse mac de la machine d'où on envoie, et le protocole (udp, tcp, ARP...).

En plus concret, cela correspond à ça sur un wireshark : 

>6419.831183    PcsCompu_ba:6d:8d   Broadcast   ARP 60  Who has 10.3.1.254? Tell 10.3.1.11

Le postier va donc nous dire de mettre la lettre dans une boite au lettre (à l'adresse MAC enregistrée).

>6419.831236    0a:00:27:00:00:12   PcsCompu_ba:6d:8d   ARP 42  10.3.1.254 is at 0a:00:27:00:00:12

Puis, la lettre arrive dans un autre bureau de poste (10.3.2.254). Le postier va donc chercher à savoir à qui, il doit donner cette lettre.

En vrai, ça donne : 

>6430.563122  PcsCompu_f1:d0:0c   Broadcast   ARP 60  Who has 10.3.2.11? Tell 10.3.2.254

Il va donc, grâce à l'adresse écrite sur la lettre, pouvoir livrer cette lettre au domicile voulu.

En plus concret ça donne ça : 

>6430.563175  PcsCompu_16:98:9d   Broadcast   ARP 60  Who has 10.3.2.11? Tell 10.3.2.254

#### Entracte

-Configuration de router

Carte NAT activé sur router : 

```
[chris@router ~]$ sudo ifup enp0s3
```

```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:df:5c:29 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
       valid_lft 80455sec preferred_lft 80455sec
```

Le NAT activé sur router : 

```
[chris@router ~]$ sudo firewall-cmd --add-masquerade --permanent
success
[chris@router ~]$ sudo firewall-cmd --reload
success
```

-Configuration de client 

Route statique par défaut : 

```
[chris@client1 ~]$ ip r s
default via 10.3.1.254 dev enp0s8 proto static metric 100
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 100
10.3.2.0 via 10.3.1.254 dev enp0s8 proto static metric 100
10.3.2.0/24 via 10.3.1.254 dev enp0s8
```

Configuration d'un server DNS : 

```
[chris@client1 ~]$ sudo nano /etc/resolv.conf
[chris@client1 ~]$ sudo cat /etc/resolv.conf
# Generated by NetworkManager
search auvence.co tp3
nameserver 1.1.1.1
nameserver 8.8.8.8
```

Pour vérifier l'acces a internet on va ping 8.8.8.8 : 

```
[chris@client1 ~]$  ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=52 time=27.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=52 time=23.7 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=52 time=24.9 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=52 time=24.4 ms
^C
--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3015ms
rtt min/avg/max/mdev = 23.769/25.154/27.410/1.368 ms
```

Le ping sur 8.8.8.8 est bien fonctionnel.

Ensuite, on va tester la resolution du nom de DNS. On va essayer avec la commande "dig". Si elle n'est pas présente il faut l'installer grace a cette commande : 

>$ sudo yum install bind-utils

On va esseyer pouvoir maintenant utiliser cette commande sur google.com : 

```
[chris@client1 ~]$ dig google.com

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-9.P2.el7 <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 9683
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1452
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             185     IN      A       216.58.213.174

;; Query time: 25 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Tue Mar 17 03:48:54 CET 2020
;; MSG SIZE  rcvd: 55
```




Ensuite, on va installer deux packages : 

```
[chris@client1 ~]$ sudo yum install -y epel-release
[sudo] password for chris:
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: centos.crazyfrogs.org
 * extras: centos.crazyfrogs.org
 * updates: ftp.pasteur.fr
Resolving Dependencies
--> Running transaction check
---> Package epel-release.noarch 0:7-11 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

========================================================================================================================
 Package                          Arch                       Version                   Repository                  Size
========================================================================================================================
Installing:
 epel-release                     noarch                     7-11                      extras                      15 k

Transaction Summary
========================================================================================================================
Install  1 Package

Total download size: 15 k
Installed size: 24 k
Downloading packages:
epel-release-7-11.noarch.rpm                                                                     |  15 kB  00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : epel-release-7-11.noarch                                                                             1/1
  Verifying  : epel-release-7-11.noarch                                                                             1/1

Installed:
  epel-release.noarch 0:7-11

Complete!
```

```
[chris@client1 ~]$ sudo yum install -y sl
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                                                             |  20 kB  00:00:00
 * base: centos.crazyfrogs.org
 * epel: mir01.syntis.net
 * extras: centos.crazyfrogs.org
 * updates: ftp.pasteur.fr
http://mirror.in2p3.fr/pub/epel/7/x86_64/repodata/repomd.xml: [Errno 12] Timeout on http://mirror.in2p3.fr/pub/epel/7/x86_64/repodata/repomd.xml: (28, 'Connection timed out after 30002 milliseconds')
Trying other mirror.
epel                                                                                             | 5.3 kB  00:00:00
(1/3): epel/x86_64/group_gz                                                                      |  95 kB  00:00:03
(2/3): epel/x86_64/updateinfo                                                                    | 1.0 MB  00:00:03
(3/3): epel/x86_64/primary_db                                                                    | 6.7 MB  00:00:17
Resolving Dependencies
--> Running transaction check
---> Package sl.x86_64 0:5.02-1.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

========================================================================================================================
 Package                 Arch                        Version                            Repository                 Size
========================================================================================================================
Installing:
 sl                      x86_64                      5.02-1.el7                         epel                       14 k

Transaction Summary
========================================================================================================================
Install  1 Package

Total download size: 14 k
Installed size: 17 k
Downloading packages:
warning: /var/cache/yum/x86_64/7/epel/packages/sl-5.02-1.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 352c64e5: NOKEY
Public key for sl-5.02-1.el7.x86_64.rpm is not installed
sl-5.02-1.el7.x86_64.rpm                                                                         |  14 kB  00:00:00
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
Importing GPG key 0x352C64E5:
 Userid     : "Fedora EPEL (7) <epel@fedoraproject.org>"
 Fingerprint: 91e9 7d7c 4a5e 96f1 7f3e 888f 6a2f aea2 352c 64e5
 Package    : epel-release-7-11.noarch (@extras)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : sl-5.02-1.el7.x86_64                                                                                 1/1
  Verifying  : sl-5.02-1.el7.x86_64                                                                                 1/1

Installed:
  sl.x86_64 0:5.02-1.el7

Complete!
```

Puis on va lancer la locomotive !!!!!!! tchou! tchoouuuuu!!!!!

Avec cette commande : 

>sl
(vu que j'adore cette locomotive je me permet de l'inserer ici :). )

```
                                      (@@) (  ) (@)  ( )  @@    ()    @     O     @     O      @                                                         (   )                                                                                                       (@@@@)                                                                                                               (    )                                                                                
                                (@@@)
                               ====        ________                ___________
                           _D _|  |_______/        \__I_I_____===__|_________|
                            |(_)---  |   H\________/ |   |        =|___ ___|      _________________
                            /     |  |   H  |  |     |   |         ||_| |_||     _|                \_____A
                           |      |  |   H  |__--------------------| [___] |   =|                        |
                           | ________|___H__/__|_____/[][]~\_______|       |   -|                        |
                           |/ |   |-----------I_____I [][] []  D   |=======|____|________________________|_
                         __/ =| o |=-~~\  /~~\  /~~\  /~~\ ____Y___________|__|__________________________|_
                          |/-=|___|=    ||    ||    ||    |_____/~\___/          |_D__D__D_|  |_D__D__D_|
                           \_/      \O=====O=====O=====O_/      \_/               \_/   \_/    \_/   \_/                

```

## III. Plus de tcpdump

### 1. TCP et UDP

#### A. Warm-up

On veut effectuer une connexion nc entre client et server. Pour ça, on va ouvrir un nouveau port sur server. On veut que le "serveur" soit server et que le "client" soit client. De plus, le port doit etre en tcp.

Pour ouvrir un port en tcp il faut entrer la commande ci-dessous : 
>sudo firewall-cmd --add-port=9999/tcp --permanent
Puis reload : 
>sudo firewall-cmd --reload

Port 9999/tcp ouvert sur server : 

```
[chris@server1 ~]$ sudo firewall-cmd --add-port=9999/tcp --permanent
[sudo] password for chris:
Sorry, try again.
[sudo] password for chris:
success
[chris@server1 ~]$ sudo firewall-cmd --reload
success
```

>sudo firewall-cmd --list-all

On entre cette commande pour vérifier que l'ajout du nouveau port a bien été fait : 

```
[chris@server1 ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client ssh
  ports: 7777/tcp 9999/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

Maintenant on va faire la connection nc entre les 2 vms. Pour cela, dans server on va entrer cette commande : 
>nc -l -t -p 9999 

et dans client, cette commande : 
>nc -t 10.3.2.11 9999

On a donc ceci : 

```
[chris@server1 ~]$ nc -l -t -p 9999
slt
slt
```

server envoie un message a client.

```
[chris@client1 ~]$ nc -t 10.3.2.11 9999
slt
slt
```

Et client reçoit le message.

Maintenant, on va faire la même chose en UDP.
Donc on va rentrer les meme commandes sauf une qui change un peu : 
>sudo firewall-cmd --add-port=9999/udp --permanent

La seule chose qui change dans la commande et qu'on a remplacé tcp par udp.

On entre la commande et on vérifie que les modifications sont bien faites : 

```
[chris@server1 ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client ssh
  ports: 7777/tcp 9999/tcp 9999/udp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

Le port a bien été ajouté.

Ensuite on rentre les meme commandes nc et on essaye le petit chat.

Server envoie un message : 

```
[chris@server1 ~]$ nc -l -u -p 9999
salam
```

Et client le reçoit : 

```
[chris@client1 ~]$ nc -u 10.3.2.11 9999
salam
```

#### B. Analyse de trames

##### TCP

On lance tcpdump sur router. On envoie un message depuis client vers server. Puis, on coupe la connexion tcpdump. Ensuite, on va dans le wireshark.
Dans le wireshark, on peut constater d'abord, le 3-way handshake TCP. 

```
3   19.408766    10.3.1.11   10.3.2.11   TCP 90  34938 → 9999 [SYN] Seq=1 

4   19.409231    10.3.2.11   10.3.1.11   TCP 90  9999 → 34938 [SYN, ACK] Seq=0 Ack=1

5   19.410565    10.3.1.11   10.3.2.11   TCP 54  34938 → 9999 [ACK] Seq=1 Ack=1 
```

On a donc : 

[SYN] = Demande de synchronisation
[SYN, ACK] = Acceptation de la synchronisation
[ACK] = Validation de l'acceptation

Ensuite, on constate d'autre message qui s'envoient. On remarque donc une suite d'échange : 

```
19.450543    10.3.1.11   10.3.2.11   TCP 92  33794 → 9999 [PSH, ACK] Seq=1 Ack=1 

19.500231    10.3.2.11   10.3.1.11   TCP 54  9999 → 33794 [ACK] Seq=1 Ack=7 

```

[PSH] = Envoie de donnée
[ACK] = Accusé de reception

Puis, pour finir la connexion, d'autre messageont envoyés :  

```
20.121342   10.3.2.11   10.3.1.11   TCP 54  9999 → 33794 [FIN, ACK] Seq=1 Ack=22 

20.121562   10.3.1.11   10.3.2.11   TCP 54  33794 → 9999 [ACK] Seq=22 Ack=2 

20.121783   10.3.1.11   10.3.2.11   TCP 54  33794 → 9999 [FIN, ACK] Seq=22 Ack=2 

20.121942   10.3.2.11   10.3.1.11   TCP 54  9999 → 33794 [ACK] Seq=2 Ack=23 
```

[FIN] = Fin de la réception
[ACK] = toujours celui qui receptionne

##### UDP

Ici aussi, on lance un tcpdump. On effectue un echange entre client et server. Puis on coupe le tcpdump. On va ensuite sur le wireshark et on a : 

```
3.130183    10.3.1.11   10.3.2.11   UDP 50  41261 → 9999 Len=5

3.988314    10.3.1.11   10.3.2.11   UDP 50  41261 → 9999 Len=5

4.906879    10.3.1.11   10.3.2.11   UDP 50  41261 → 9999 Len=4

```

Le protocole TCP va prevenir de l'arrivé des données, et nous dis si elles ont bien été reçu, grâce a l'accusé de reception.
Le protocole UDP accepte les messages tels qu'ils sont, sans prevenir le destinataire.

### 2. SSH

On cherche a se connecter en ssh depuis client, vers server. 
On entre donc la commande magique : 
>ssh 10.3.2.11 -p 7777

Et on voit ce que ça donne :).

```
[chris@client1 ~]$ ssh 10.3.2.11 -p 7777
The authenticity of host '[10.3.2.11]:7777 ([10.3.2.11]:7777)' can't be established.
ECDSA key fingerprint is SHA256:qb7pzC5iSjd3Okw3r33AYJHc2gnNmLvpoIR+1J1pfCs.
ECDSA key fingerprint is MD5:35:43:f0:8f:bb:d1:7f:0d:87:a5:a1:e7:9f:1a:cb:88.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[10.3.2.11]:7777' (ECDSA) to the list of known hosts.
chris@10.3.2.11's password:
Last login: Tue Mar 17 06:00:40 2020 from 10.3.2.1
[chris@server1 ~]$ j'ai réussi oui :)
```

On ouvre le wireshark vu qu'on a activé le tcpdump et on regarde : 

```
6.326319    10.3.1.11   10.3.2.11   TCP 74  41534 → 7777 [SYN] Seq=0 

6.337513    10.3.2.11   10.3.1.11   TCP 74  7777 → 41534 [SYN, ACK] Seq=0 Ack=1 

6.348984    10.3.1.11   10.3.2.11   TCP 66  41534 → 7777 [ACK] Seq=1 Ack=1 
```

On remarque la presence du protocole TCP. Donc SSH utilise le protocole SSH.