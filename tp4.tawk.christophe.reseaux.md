# TP 4 - Cisco, Routage, DHCP

## I. Topologie 1 : simple

### 2. Mise en place

#### B. Définition d'IPs statiques

##### admin

On va d'abord configurer la vm centos sur virtualbox.
Pour ce faire, on va cloner une vm depuis le patron. Il faudra que cette vm est : 

-Le selinux desactivé : 

```
[chris@localhost ~]$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31
```
Current mode est bien en permissive, donc le selinux est bien désactivé.

-Traceroute d'installé : 

```
[chris@localhost ~]$ sudo yum -y install traceroute
[sudo] password for chris:
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: ftp.pasteur.fr
 * extras: distrib-coffee.ipsl.jussieu.fr
 * updates: distrib-coffee.ipsl.jussieu.fr
base                                                                                             | 3.6 kB  00:00:00
extras                                                                                           | 2.9 kB  00:00:00
updates                                                                                          | 2.9 kB  00:00:00
(1/2): extras/7/x86_64/primary_db                                                                | 164 kB  00:00:00
(2/2): updates/7/x86_64/primary_db                                                               | 7.6 MB  00:00:09
Package 3:traceroute-2.0.22-2.el7.x86_64 already installed and latest version
Nothing to do
```

Traceroute est déjà installé sur la vm.

-Créer une ip statique : 

```
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:38:fc:d7 brd ff:ff:ff:ff:ff:ff
    inet 10.4.1.11/24 brd 10.4.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe38:fcd7/64 scope link
       valid_lft forever preferred_lft forever
```

L'ip 10.4.1.11 a bien été créé.

-Définir un nom d'hôte : 

```
[chris@localhost ~]$ echo 'admin1' | sudo tee /etc/hostname
admin1
[chris@localhost ~]$ sudo hostname admin1
[chris@localhost ~]$ hostname
admin1
```

Le nom de domaine a été defini sur "admin1"

##### router

Definition des ips statiques en fonction du tableau d'adressage : 

```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.4.1.254      YES manual up                    up
FastEthernet1/0            10.4.2.254      YES manual up                    up

```

On peut voir que sur les 2 interfaces, les adresses ips ont bien été défini.

Définition d'un nom : 

![](https://i.imgur.com/9wZzofq.png)

Je decide de laisser ce nom pour le router.

##### guest

Definition de l'ip statique en fonction du tableau d'adressage : 

```
guest1> ip 10.4.2.11 255.255.255.0 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.11 255.255.255.0 gateway 10.4.2.254
```

Définition d'un nom : 

![](https://i.imgur.com/haqNec0.png)

Je l'appelle guest1.

##### VERIFICATION

guest peut joindre le router. On teste avec un ping

```
guest1> ping 10.4.2.254
84 bytes from 10.4.2.254 icmp_seq=1 ttl=255 time=8.206 ms
84 bytes from 10.4.2.254 icmp_seq=2 ttl=255 time=3.152 ms
84 bytes from 10.4.2.254 icmp_seq=3 ttl=255 time=8.012 ms
84 bytes from 10.4.2.254 icmp_seq=4 ttl=255 time=9.056 ms
84 bytes from 10.4.2.254 icmp_seq=5 ttl=255 time=10.831 ms

```

admin peut joindre router. On teste avec un ping.

![](https://i.imgur.com/L2ENoVG.png)

router peut joindre les deux autres machines. On teste avec un ping sur chacune des vms.

```
R1#ping 10.4.1.11

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.4.1.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 36/45/68 ms

R1#ping 10.4.2.11

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.4.2.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/32/36 ms
```

Ici, on a les ping de router vers les 2 autres machines.

Maintenant, on va vérifier la table ARP de router.
D'abord, on vérifie que guest et admin sont bien présents dans la table ARP de router : 

```
R1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.4.1.11               4   0800.2738.fcd7  ARPA   FastEthernet0/0
Internet  10.4.2.11               8   0050.7966.6800  ARPA   FastEthernet1/0
Internet  10.4.1.254              -   cc01.04f4.0000  ARPA   FastEthernet0/0
Internet  10.4.2.254              -   cc01.04f4.0010  ARPA   FastEthernet1/0
```

On compare les mac. Entre router et admin : 

router
```
Internet  10.4.1.11               4   0800.2738.fcd7  ARPA   FastEthernet0/0
```

admin
```
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:38:fc:d7 brd ff:ff:ff:ff:ff:ff
    inet 10.4.1.11/24 brd 10.4.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe38:fcd7/64 scope link
       valid_lft forever preferred_lft forever
```

On constate effectivement que les adresses mac sont les même, donc la verification sur admin est bonne.

Entre router et guest : 

router

```
Internet  10.4.2.254              -   cc01.04f4.0010  ARPA   FastEthernet1/0
```

guest
```
guest1> show arp

cc:01:04:f4:00:10  10.4.2.254 expires in 106 seconds

```

On constate effectivement que les adresses mac sont les même, donc la verification est bonne aussi pour guest.

#### C. Routage

On ajoute une route sur admin pour joindre guest.

On modifie le fichier "/etc/sysconfig/network-scripts/route-enp0s8" et on entre 10.4.2.0 via 10.4.1.254 dev enp0s8.

Pour guest, on ne peut pas ajouter une route par defaut. Donc on va lui ajouter une passerelle par défaut, comme fait précedemment.

```
guest1> ip 10.4.2.11 255.255.255.0 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.11 255.255.255.0 gateway 10.4.2.254
```

On a bien ajouté comme passerelle par defaut le routeur afin de pouvoir communiquer entre les 2 vms.

Maintenant, on va ping les vms entre elles. D'abord, de guest vers admin (on le fait avec un ping) : 

```
guest1> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=20.898 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=18.445 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=14.717 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=16.795 ms
84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=17.526 ms

```

Et de admin vers guest (on le fait aussi avec un ping) : 

![](https://i.imgur.com/apKF2mW.png)

On verifie avec traceroute que les paquets transitent bien par router.
Pour ce faire, on utilise la commande traceroute. Sur guest, elle s'ecrit comme ca.

>trace 10.4.1.11

Ca donne : 

```
guest1> trace 10.4.1.11
trace to 10.4.1.11, 8 hops max, press Ctrl+C to stop
 1   10.4.2.254   4.512 ms  9.628 ms  10.656 ms
 2   *10.4.1.11   19.623 ms (ICMP type:3, code:10, Host administratively prohibited)
```

Pour admin, la commande est celle-ci dessous.

>traceroute 10.4.2.11

Ca donne : 

![](https://i.imgur.com/np1EYVe.png)

Sur les 2 vms, les paquets transitent bien vers router.

## II. Topologie 2 : dumb switches

### 2. Mise en place

#### A. Topologie GNS3

![](https://i.imgur.com/xUa5pqr.png)

#### C. Vérification


Afin de verifier que les vms peuvent encore se joindre avec l'ajout des switchs, on va faire des pings entre les vms ainsi que des traceroute.

Ping de guest vers admin : 

```
guest1> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=31.098 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=18.993 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=18.138 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=13.736 ms
84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=20.879 ms

```

Ping de admin vers guest : 

![](https://i.imgur.com/yidbi5r.png)

traceroute de guest vers admin : 

```
guest1> trace 10.4.1.11
trace to 10.4.1.11, 8 hops max, press Ctrl+C to stop
 1   10.4.2.254   7.481 ms  8.891 ms  10.199 ms
 2   *10.4.1.11   20.202 ms (ICMP type:3, code:10, Host administratively prohibited)
```

traceroute de admin vers guest : 

![](https://i.imgur.com/FpVn04O.png)

## III. Topologie 3 : adding nodes and NAT

### B. VPCS

On met 2 autres VPCS que l'on renomme (ici, guest 2 et guest 3).
Puis on fait les même configuration que pour guest 1.

Sur guest 2

```
guest2> ip 10.4.2.12 255.255.255.0 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.12 255.255.255.0 gateway 10.4.2.254

```

Sur guest 3

```
guest3> ip 10.4.2.13 255.255.255.0 10.4.2.254
Checking for duplicate address...
PC1 : 10.4.2.13 255.255.255.0 gateway 10.4.2.254
```

Ensuite, on fait un ping de guest 2 vers admin, et la même chose pour guest 3.
On fait un ping vour vérifier que guest 2 et 3 joignent bien admin.

Ping de guest 2 vers admin

```
guest2> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=20.214 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=18.478 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=11.604 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=12.366 ms
84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=11.322 ms

```

Ping de guest 3 vers admin

```
guest3> ping 10.4.1.11
84 bytes from 10.4.1.11 icmp_seq=1 ttl=63 time=20.386 ms
84 bytes from 10.4.1.11 icmp_seq=2 ttl=63 time=12.605 ms
84 bytes from 10.4.1.11 icmp_seq=3 ttl=63 time=17.031 ms
84 bytes from 10.4.1.11 icmp_seq=4 ttl=63 time=13.471 ms
84 bytes from 10.4.1.11 icmp_seq=5 ttl=63 time=16.636 ms
```

Maintenant, pour bien vérifier que les vms vms joignent bien admin, on va voir la table ARP de guest 2 et 3.

Table ARP de guest 2 : 

```
guest2> show arp

cc:01:04:7f:00:10  10.4.2.254 expires in 110 seconds

```

Table ARP de guest 3 : 

```
guest3> show arp

cc:01:04:7f:00:10  10.4.2.254 expires in 105 seconds

```

#### C. Accès WAN

##### Donner un accès WAN au routeur

##### Configurer le router

On veut donner une ip dhcp au router. Pour ce faire, il faut entrer avec conf t dans FastEthernet2/0, et entrer la commande ci dessous.

>ip adress dhcp

On le fait et on regarde maintenant l'adresse ip de FastEthernet2/0 : 

```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.4.1.254      YES NVRAM  up                    up  
FastEthernet1/0            10.4.2.254      YES NVRAM  up                    up  
FastEthernet2/0            192.168.122.233 YES DHCP   up                    up  
```

On constate que le router sur l'interface branchée au NAT a bien une adresse ip en dhcp.

configuration d'un NAT simple : 

```
R1#conf t
R1(config)#ip nat FastEthernet0/0
R1(config-if)#ip nat inside
R1(config-if)#exit
R1(config)#interface FastEthernet1/0
R1(config-if)#ip nat inside
R1(config-if)#exit
R1(config)#interface FastEthernet 2/0
R1(config-if)#ip nat outside
R1(config-if)#exit
R1(config)#ip nat inside source list 1 interface fastEthernet 2/0 overload
R1(config)#exit
```
Les interfaces internes, sont les lans, ceux qui sont connectés par câble à la machine en reseaux local. L'interfaces externe est celle connectée a internet.
De plus, on a une ligne qui apparaît après cette suite de commande dans l'affichage des adresses ip du router : 

```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.4.1.254      YES NVRAM  up                    up  
FastEthernet1/0            10.4.2.254      YES NVRAM  up                    up  
FastEthernet2/0            192.168.122.233 YES DHCP   up                    up  
NVI0                       unassigned      NO  unset  up                    up 
```

Cette ligne est la derniere, qui n'a aucune ip assigné.

##### Configurer les clients

Il faut ajouter les routes par defauts sur guest 1, 2, 3 et sur admin. Les routes sur guest 1, 2, 3 sont les gateways comme dit precedemment, car sur cisco, on ne peut pas ajouter de route par defaut. Donc, on ajoute des passerelles par defaut.

sur guest 1 
>ip 10.4.2.11 255.255.255.0 10.4.2.254

sur guest 2
>ip 10.4.2.12 255.255.255.0 10.4.2.254

sur guest 3
>ip 10.4.2.13 255.255.255.0 10.4.2.254

Puis sur admin, on modifie le fichier "/etc/sysconfig/network-scripts/route-enp0s8" et on entre 10.4.2.0 via 10.4.1.254 dev enp0s8. Cela à déjà été fait plus tôt.

Pour configurer l'utilisation d'un server DNS, on doit faire des modifications sur les vms admin, et guest (1, 2, 3).

Pour admin, on modifie le fichier resolv.conf, et on ajoute la ligne "name server 1.1.1.1".
Ca donne ça : 

![](https://i.imgur.com/wB441DC.png)

Pour les guest (1, 2, 3), il faut utiliser la commande "ip dns 1.1.1.1"
Il faut donc le faire sur toutes les vms.

Guest 1, 2, 3
>ip dns 1.1.1.1

##### Vérifications

Pour prouver qu'on a bien internet, on va ping un site internet. 
On va d'abord le faire sur admin. On va ping www.google.com

![](https://i.imgur.com/h2aquL9.png)

Maintenant, on va ping www.google.com depuis guest 1, 2, 3.

Depuis guest 1 : 

```
guest1> ping www.google.com
www.google.com resolved to 216.58.198.196
84 bytes from 216.58.198.196 icmp_seq=1 ttl=50 time=38.286 ms
84 bytes from 216.58.198.196 icmp_seq=2 ttl=50 time=50.908 ms
84 bytes from 216.58.198.196 icmp_seq=3 ttl=50 time=50.069 ms
84 bytes from 216.58.198.196 icmp_seq=4 ttl=50 time=73.480 ms
84 bytes from 216.58.198.196 icmp_seq=5 ttl=50 time=39.303 ms
```

Depuis guest 2 : 

```
guest2> ping www.google.com
www.google.com resolved to 216.58.198.196
84 bytes from 216.58.198.196 icmp_seq=1 ttl=50 time=51.967 ms
84 bytes from 216.58.198.196 icmp_seq=2 ttl=50 time=55.083 ms
84 bytes from 216.58.198.196 icmp_seq=3 ttl=50 time=37.252 ms
84 bytes from 216.58.198.196 icmp_seq=4 ttl=50 time=40.072 ms
84 bytes from 216.58.198.196 icmp_seq=5 ttl=50 time=33.831 ms
```

Depuis guest 3 : 

```
guest3> ping www.google.com
www.google.com resolved to 216.58.198.196
84 bytes from 216.58.198.196 icmp_seq=1 ttl=50 time=40.081 ms
84 bytes from 216.58.198.196 icmp_seq=2 ttl=50 time=39.961 ms
84 bytes from 216.58.198.196 icmp_seq=3 ttl=50 time=35.331 ms
84 bytes from 216.58.198.196 icmp_seq=4 ttl=50 time=34.739 ms
84 bytes from 216.58.198.196 icmp_seq=5 ttl=50 time=32.765 ms
```

Maintenant, on va ping www.google.com depuis router. On ne peut pas ping directement www.google.com, donc on va utiliser l'adresse ip recherché par le ping des autres vms.
Donc, on va utiliser l'ip 172.217.19.228, celle qu'admin a ping, pour ping www.google.com : 

```
R1#ping 172.217.19.228

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.217.19.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 96/97/104 ms
```

## IV. Topologie 4 : home-made DHCP

### 2. Mise en place

#### B. Setup de la VM

Après avoir fait le setup de la vm, il faut vérifier qu'on a des port dhcp en écoute. Pour ce faire, on utilise la commande **ss -lnu** : 

![](https://i.imgur.com/AIJLO7d.png)

Les ports utilisés par le DHCP, sont les ports 67 et 68. Ici, le port 67 correspond au DHCP, qui est en écoute.

Pour attribuer une IP dhcp a un guest, il faut entrer la commande "ip dhcp" sur le guest voulu. Ici, on va le faire sur le guest 1.

```
guest1> ip dhcp
DDORA IP 10.4.2.100/24 GW 10.4.2.254
```

On constate bien qu"on récupere l'ip, mais en plus on récupère l'adresse de la passerelle. Ici, l'adresse de la passerelle récuperé depuis guest 1 est 10.4.2.254. On a comme indication, "DORA".

Maintenant, on veut mettre en évidence les échanges dhcp, qui est reconnu par l'abreviations "DORA". Pour ce faire, il faut lancer un wireshark pour capter le message 'DORA'. Pour ce faire on lance le wireshark depuis le guest où, on lance l'ip dhcp (ici le 1), et on regarde. On veut obtenir les messages Discover, Offer, Request, Acknowledge. On peut bien les voir ici : 

![](https://i.imgur.com/2T0M2qu.png)
