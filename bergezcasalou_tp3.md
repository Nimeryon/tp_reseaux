# BERGEZCASALOU TP3
## Compte rendu TP

### Routage, ARP, Spéléologie réseau
#### Préparation de l'environnement 
##### 1. Mise à jour du patron

- mise à jour, ajout des paquets faits et selinux désactivé

##### 2. Mise en place du lab

- Carte NAT désactivé : ```ip a```

sur client1 :
```bash
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:a2:40:a4 brd ff:ff:ff:ff:ff:ff
```

sur server1 : 
```bash
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:ba:a8:15 brd ff:ff:ff:ff:ff:ff
```
    
sur router : 
```bash
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:dc:92:4b brd ff:ff:ff:ff:ff:ff
```
    
- Serveur SSH fonctionnel qui écoute sur le port 7777/tcp : ```ss -ltunp```

sur client1 : 
```bash
[user@client1 ~]$ ss -ltunp
Netid  State      Recv-Q Send-Q            Local Address:Port                           Peer Address:Port
tcp    LISTEN     0      100                   127.0.0.1:25                                        *:*
tcp    LISTEN     0      128                           *:7777                                      *:*
tcp    LISTEN     0      100                       [::1]:25                                     [::]:*
tcp    LISTEN     0      128                        [::]:7777                                   [::]:*
```

sur server1 : 
```bash
[user@server1 ~]$ ss -ltunp
Netid  State      Recv-Q Send-Q            Local Address:Port                           Peer Address:Port
tcp    LISTEN     0      100                   127.0.0.1:25                                        *:*
tcp    LISTEN     0      128                           *:7777                                      *:*
tcp    LISTEN     0      100                       [::1]:25                                     [::]:*
tcp    LISTEN     0      128                        [::]:7777                                   [::]:*
```

sur router : 
```bash
[user@router ~]$ ss -ltunp
Netid  State      Recv-Q Send-Q            Local Address:Port                           Peer Address:Port
tcp    LISTEN     0      100                   127.0.0.1:25                                        *:*
tcp    LISTEN     0      128                           *:7777                                      *:*
tcp    LISTEN     0      100                       [::1]:25                                     [::]:*
tcp    LISTEN     0      128                        [::]:7777                                   [::]:*
```

- Pare-feu activé et configuré : ```sudo firewall-cmd --list-all```

sur client1 : 
```bash
[user@client1 ~]$ sudo firewall-cmd --list-all
[sudo] password for user:
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

sur server1 : 
```bash
[user@server1 ~]$ sudo firewall-cmd --list-all
[sudo] password for user:
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

sur router : 
```bash
[user@router ~]$ sudo firewall-cmd --list-all
[sudo] password for user:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8 enp0s9
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

- Nom configuré : ```hostname --fqdn```

sur client1 : 
```bash
[user@client1 ~]$ hostname --fqdn
client1.net1.tp3
```

sur server1 : 
```bash
[user@server1 ~]$ hostname --fqdn
server1.net2.tp3
```

sur router : 
```bash
[user@router ~]$ hostname --fqdn
router.tp3
```

- Fichiers /etc/hosts de toutes les machines configurés : ```cat /etc/hosts```

sur client1 : 
```bash
[user@client1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11 client1
10.3.2.11 server1
10.3.1.254 router
10.3.2.254 router
```

sur server1 : 
```bash
[user@server1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11 client1
10.3.2.11 server1
10.3.1.254 router
10.3.2.254 router
```

sur router : 
```bash
[user@router ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11 client1
10.3.2.11 server1
10.3.1.254 router
10.3.2.254 router
```

- Réseaux et adressage des machines : ```ping```

client1 ping router 
```bash
[user@client1 ~]$ ping router
PING router (10.3.1.254) 56(84) bytes of data.
64 bytes from router (10.3.1.254): icmp_seq=1 ttl=64 time=0.374 ms
64 bytes from router (10.3.1.254): icmp_seq=2 ttl=64 time=0.288 ms
64 bytes from router (10.3.1.254): icmp_seq=3 ttl=64 time=0.358 ms
64 bytes from router (10.3.1.254): icmp_seq=4 ttl=64 time=0.363 ms
64 bytes from router (10.3.1.254): icmp_seq=5 ttl=64 time=0.330 ms
^C
--- router ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4004ms
rtt min/avg/max/mdev = 0.288/0.342/0.374/0.036 ms
[user@client1 ~]$
```

router ping client1
```bash
[user@router ~]$ ping client1
PING client1 (10.3.1.11) 56(84) bytes of data.
64 bytes from client1 (10.3.1.11): icmp_seq=1 ttl=64 time=0.304 ms
64 bytes from client1 (10.3.1.11): icmp_seq=2 ttl=64 time=0.311 ms
64 bytes from client1 (10.3.1.11): icmp_seq=3 ttl=64 time=0.349 ms
64 bytes from client1 (10.3.1.11): icmp_seq=4 ttl=64 time=0.305 ms
^C
--- client1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3001ms
rtt min/avg/max/mdev = 0.304/0.317/0.349/0.022 ms
```

server1 ping router
```bash
[user@server1 ~]$ ping router
PING router (10.3.2.254) 56(84) bytes of data.
64 bytes from router (10.3.2.254): icmp_seq=1 ttl=64 time=0.304 ms
64 bytes from router (10.3.2.254): icmp_seq=2 ttl=64 time=0.275 ms
64 bytes from router (10.3.2.254): icmp_seq=3 ttl=64 time=0.337 ms
64 bytes from router (10.3.2.254): icmp_seq=4 ttl=64 time=0.316 ms
^C
--- router ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
rtt min/avg/max/mdev = 0.275/0.308/0.337/0.022 ms
```

router ping server1
```bash
[user@router ~]$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=64 time=0.722 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=64 time=0.360 ms
64 bytes from server1 (10.3.2.11): icmp_seq=3 ttl=64 time=0.349 ms
64 bytes from server1 (10.3.2.11): icmp_seq=4 ttl=64 time=0.284 ms
^C64 bytes from server1 (10.3.2.11): icmp_seq=5 ttl=64 time=0.302 ms
64 bytes from server1 (10.3.2.11): icmp_seq=6 ttl=64 time=0.389 ms
^C
--- server1 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5000ms
rtt min/avg/max/mdev = 0.284/0.401/0.722/0.147 ms
```

#### I. Mise en place du routage 
##### 1. Configuration du routage sur router

```bash
[user@router ~]$ sudo sysctl -w net.ipv4.conf.all.forwarding=1
[sudo] password for user:
net.ipv4.conf.all.forwarding = 1
```

##### 2. Ajout des routes statics

- ip route show

```bash
[user@client1 ~]$ ip r s
default via 10.3.1.254 dev enp0s8 proto static metric 101
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 101
10.3.2.11 via 10.3.1.254 dev enp0s8 proto static metric 101
```

```bash
[user@server1 ~]$ ip r s
default via 10.3.2.254 dev enp0s8 proto static metric 101
10.3.1.11 via 10.3.2.254 dev enp0s8 proto static metric 101
10.3.2.0/24 dev enp0s8 proto kernel scope link src 10.3.2.11 metric 101
```

- ping :

```bash
[user@client1 ~]$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=63 time=0.915 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=63 time=0.563 ms
64 bytes from server1 (10.3.2.11): icmp_seq=3 ttl=63 time=0.541 ms
64 bytes from server1 (10.3.2.11): icmp_seq=4 ttl=63 time=0.554 ms
^C
--- server1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3011ms
rtt min/avg/max/mdev = 0.541/0.643/0.915/0.158 ms
```

```bash
[user@server1 ~]$ ping client1
PING client1 (10.3.1.11) 56(84) bytes of data.
64 bytes from client1 (10.3.1.11): icmp_seq=1 ttl=63 time=0.724 ms
64 bytes from client1 (10.3.1.11): icmp_seq=2 ttl=63 time=0.551 ms
64 bytes from client1 (10.3.1.11): icmp_seq=3 ttl=63 time=0.551 ms
64 bytes from client1 (10.3.1.11): icmp_seq=4 ttl=63 time=0.567 ms
^C
--- client1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 0.551/0.598/0.724/0.074 ms
```


##### 3. Comprendre le routage

|   | MAC src  | MAC dst  | IP src  | IP dst  |
|---|---|---|---|---|
| Dans net1 (trame qui entre dans router)  | 0a:00:27:00:00:0f  | 0a:00:27:00:00:0f  | 10.3.1.11  | 10.3.1.254  |
| Dans net2 (trame qui sort de router)  | 0a:00:27:00:00:18  | 0a:00:27:00:00:18  | 10.3.2.1  | 10.3.2.254  |


#### II. ARP
##### 1. tables ARP

```bash
[user@client1 ~]$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=63 time=1.52 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=63 time=0.599 ms
64 bytes from server1 (10.3.2.11): icmp_seq=3 ttl=63 time=0.568 ms
64 bytes from server1 (10.3.2.11): icmp_seq=4 ttl=63 time=0.600 ms
^C
--- server1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3013ms
rtt min/avg/max/mdev = 0.568/0.823/1.527/0.407 ms
```

```bash
[user@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:18 REACHABLE  client1 demande si accessible
10.3.1.254 dev enp0s8 lladdr 08:00:27:17:c5:0f STALE    confié à router
```

```bash
[user@server1 ~]$ ip neigh show
10.3.2.254 dev enp0s8 lladdr 08:00:27:28:9c:68 STALE    router possede et demande si accessible
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:0f REACHABLE  server dit accessible
```

```bash
[user@router ~]$ ip neigh show
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE      demande si accessible
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:18 DELAY      delai
10.3.2.11 dev enp0s9 lladdr 08:00:27:cb:2f:06 STALE     dit accessible
10.3.1.11 dev enp0s8 lladdr 08:00:27:be:c2:ca STALE     transmet
10.3.2.1 dev enp0s9 lladdr 0a:00:27:00:00:0f STALE      dit accessible et recois
```
###### A. Table ARP 1

```bash
[user@client1 ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:18 REACHABLE
10.3.1.254 dev enp0s8 lladdr 08:00:27:17:c5:0f REACHABLE
```

###### B. Table ARP 2

```bash
[user@server1 ~]$ ip neigh show
10.3.2.254 dev enp0s8 lladdr 08:00:27:28:9c:68 REACHABLE
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:0f REACHABLE
```

###### C. TCPdump 1

```bash
/
```

###### D. TCPdump 2

```bash
/
```

###### E. u okay bro

```bash
/
```

#### Entracte : Donner un accès internet aux VMs
```bash
[user@client1 ~]$ sl
```
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


#### III. Plus de tcpdump

```bash
[user@client1 ~]$ nc server1 9999
salut
yop
^C
```

```bash
[user@server1 ~]$ nc -l -p 9999
salut
yop
```


