# Compte rendu TAWK Christophe

## I. Exploration locale en solo

### 1. Affichage d'informations sur la pile TCP/IP locale

#### Affichez les infos des cartes réseau de votre PC:

|    |Nom    |Adresse MAC    |Adresse IP|
|----|-------|---------------|----------|
|WI-FI| Intel(R) Dual Band Wireless-AC 3168|     A0-D3-7A-E8-EB-F3|10.33.1.246|
|Ethernet|Je n'ai pas de carte ethernet|x|x|

#### Gateway:

    commande entrée: ipconfig /all
    
Resultat:
```
            Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . : auvence.co
   Description. . . . . . . . . . . . . . : Intel(R) Dual Band Wireless-AC 3168
   Adresse physique . . . . . . . . . . . : A0-D3-7A-E8-EB-F3
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::c507:3dc8:4a41:6646%8(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.1.246(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Bail obtenu. . . . . . . . . . . . . . : jeudi 16 janvier 2020 08:57:19
   Bail expirant. . . . . . . . . . . . . : jeudi 16 janvier 2020 11:59:41
   Passerelle par défaut. . . . . . . . . : 10.33.3.253
   Serveur DHCP . . . . . . . . . . . . . : 10.33.3.254
   IAID DHCPv6 . . . . . . . . . . . : 60871546
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-22-75-30-BF-A0-D3-7A-E8-EB-F3
   Serveurs DNS. . .  . . . . . . . . . . : 10.33.10.20
                                       10.33.10.2
                                       8.8.8.8
                                       8.8.4.4
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
```

Gateway : 

        Passerelle par défaut. . . . . . . . . : 10.33.3.253

#### Trouvez comment afficher les informations sur une carte IP:

![](https://i.imgur.com/ZS3BBjs.png)

![](https://i.imgur.com/Dv1hLNF.png)


L'IP : 
    -10.33.1.246
La MAC : 
    -A0-D3-7A-E8-EB-F3
La Gateway : 
    -10.33.3.253
    
#### à quoi sert la gateway dans le réseau d'YNOV ?

Elle sert a pouvoir communiquer entre les résaux.

### 2.Modifications des informations

#### Modification d'adresse IP

`commande nmap : nmap -sn -PE 10.33.0.0/22`

Resultat:

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-01-16 12:55 Paris, Madrid
mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
Nmap scan report for 10.33.0.10
Host is up (0.73s latency).
MAC Address: 64:70:33:23:46:1A (Apple)
Nmap scan report for 10.33.0.11
Host is up (0.0030s latency).
MAC Address: 74:40:BB:2E:B8:E1 (Hon Hai Precision Ind.)
Nmap scan report for 10.33.0.16
Host is up (0.75s latency).
MAC Address: 74:EB:80:E6:EB:AA (Samsung Electronics)
Nmap scan report for 10.33.0.17
Host is up (0.013s latency).
MAC Address: C8:3D:D4:7A:CD:FF (CyberTAN Technology)
Nmap scan report for 10.33.0.18
Host is up (0.0030s latency).
MAC Address: 00:5B:94:07:67:7B (Apple)
Nmap scan report for 10.33.0.19
Host is up (0.028s latency).
MAC Address: A4:83:E7:6C:94:5D (Apple)
Nmap scan report for 10.33.0.22
Host is up (0.034s latency).
MAC Address: 8C:85:90:32:96:7D (Apple)
Nmap scan report for 10.33.0.23
Host is up (0.024s latency).
MAC Address: 38:F9:D3:81:EA:7D (Apple)
Nmap scan report for 10.33.0.27
Host is up (0.0090s latency).
MAC Address: B4:69:21:47:B9:EA (Intel Corporate)
Nmap scan report for 10.33.0.28
Host is up (0.71s latency).
MAC Address: 40:98:AD:22:D7:61 (Apple)
Nmap scan report for 10.33.0.31
Host is up (0.12s latency).
MAC Address: 40:A3:CC:83:01:DF (Intel Corporate)
Nmap scan report for 10.33.0.35
Host is up (0.0030s latency).
MAC Address: A8:86:DD:95:11:A4 (Apple)
Nmap scan report for 10.33.0.37
Host is up (0.015s latency).
MAC Address: 30:52:CB:E9:EA:D3 (Liteon Technology)
[...]
Nmap scan report for 10.33.3.213
Host is up (0.0040s latency).
MAC Address: 64:5D:86:51:69:C2 (Intel Corporate)
Nmap scan report for 10.33.3.216
Host is up (0.14s latency).
MAC Address: D8:CE:3A:91:77:3D (Xiaomi Communications)
Nmap scan report for 10.33.3.219
Host is up (0.074s latency).
MAC Address: A4:83:E7:CC:B8:7E (Apple)
Nmap scan report for 10.33.3.220
Host is up (0.075s latency).
MAC Address: C0:BD:C8:40:96:27 (Samsung Electronics)
Nmap scan report for 10.33.3.223
Host is up (0.0060s latency).
MAC Address: 48:51:B7:C2:C2:25 (Intel Corporate)
Nmap scan report for 10.33.3.229
Host is up (0.051s latency).
MAC Address: A4:83:E7:6B:BC:CE (Apple)
Nmap scan report for 10.33.3.236
Host is up (0.21s latency).
MAC Address: A0:CC:2B:1C:A1:3C (Murata Manufacturing)
Nmap scan report for 10.33.3.242
Host is up (0.0050s latency).
MAC Address: D8:F2:CA:D9:32:94 (Intel Corporate)
Nmap scan report for 10.33.3.247
Host is up (0.0040s latency).
MAC Address: 90:78:41:61:D0:46 (Intel Corporate)
Nmap scan report for 10.33.3.253
Host is up (0.0080s latency).
MAC Address: 00:12:00:40:4C:BF (Cisco Systems)
Nmap scan report for 10.33.3.254
Host is up (0.0030s latency).
MAC Address: 94:0C:6D:84:50:C8 (Tp-link Technologies)
Nmap done: 1024 IP addresses (335 hosts up) scanned in 48.81 seconds
```


Avec ce resulat, je peux voir qu'elles adresses sont disponible et donc je peux changer mon adresse en fonction de celles qui sont disponibles.


![](https://i.imgur.com/x8marD0.png)


## II. Exploration locale en duo

Faite par AURIOL Thomas et BERGEZ-CASALOU Malory.

### 3. Exploration locale en duo

`Adresse IP de Thomas : 192.168.69.194`

![](https://i.imgur.com/ilyEvEn.png)

`Adresse Ip de Malory : 192.168.69.193`

![](https://i.imgur.com/uotebla.png)

Vérification des changements : 

Pour Thomas : 

![](https://i.imgur.com/M7bF7Tw.png)

Pour Malory : 

![](https://i.imgur.com/jzrxt0n.png)

Maintenant, Thomas et Malory vont se ping : 

Thomas vers Malory:

![](https://i.imgur.com/r1zC71q.png)

Malory vers Thomas:

![](https://i.imgur.com/YT3H1Su.png)

### 4. Utilisation d'un des deux comme gateway

C'est Malory qui va envoyer le WI-FI à Thomas.

Pour tester la connectivité a Internet, Thomas a entré la commande 'curl google.fr'.
Résultat : 

![](https://i.imgur.com/G8KzmPK.png)

Pour bien voir que les requêtes passent par la passerelle choisie, on fait : 

![](https://i.imgur.com/9yidNOW.png)

### 5. Petit chat privé

Malory (PC Serveur):
![](https://i.imgur.com/38EAKho.png)

Thomas (PC Client):
![](https://i.imgur.com/CtK1Eut.png)

### 6. Wireshark

En effectuant un ping vers l'ordinateur configurer avec l'ip **192.168.137.1**, wireshark lis :
![](https://i.imgur.com/cETR7KE.png)

en utilisant **nc64.exe -l -p 8888 -u** dans le cmd wireshark lis :
![](https://i.imgur.com/J6pom2d.png)
lorsceque je reçoit un message de l'autre ordinateur.

### 7. FireWall

après avoir ajouté une nouvelle rêgle permettant les réquêtes de type echo en entrée avec ICMP type 8:
![](https://i.imgur.com/VRxPgJ6.png)
le ping fonctionne parfaitement avec le firewall activé :
![](https://i.imgur.com/CAjxtXr.png)

pour le netcat on ajoute la même rêgle en sortie avec ICMP type 0 :
![](https://i.imgur.com/kq2qeAd.png)


## III. Manipulations d'autres outils/protocoles côté client

### 1. DHCP

Pour trouver l'adresse DHCP, on fait un ipconfig /all et on a:

![](https://i.imgur.com/yWpBjIs.png)

On remarque que l'adresse DHCP est : 10.33.51.254

De plus, on peut constater que le bail se termine le mercredi 29 janvier 2020 à 14:25:35.


Pour le renouvellement du bail, il faut entrer d'abord : ipconfig /release
Cette commande va supprimer le bail actuel.

Puis, il faut entrer la commande : ipconfig /renew
Cette commande va donc regenerer le bail DHCP.

Je n'ai pas de screenshot car cela ne marche pas sur mon pc.

## 2. DNS

Pour trouver l'adresse du serveur DNS, on fait : 
ipconfig /all

et on a : 

![](https://i.imgur.com/T7dbKjZ.png)

On peut constater que l'adresse DNS est 10.33.10.20

Pour faire des requetes DNS vers google.com et ynov.com, on fait : 

![](https://i.imgur.com/G5k69dW.png)

Pour faire des requetes DNS vers des adresses IP, on fait : 

![](https://i.imgur.com/jy2Lh7b.png)

Ici, avec ces commandes, l'ordinateur ne reconnait pas les adresses IP entrées.
Cependant, avec certains sites, on peut trouver à quels sites appartiennent ces IP.