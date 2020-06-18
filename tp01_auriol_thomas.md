# Compte rendu AURIOL Thomas

---
## I. Exploration locale en solo
### 1. Affichage d'informations sur la pile TCP/IP locale

Commande :
>ipconfig / all

Pour l'adresse IP du Wifi : 
>connexion à WIFI@YNOV

Pour l'adresse IP Ethernet : 
>connexion en filaire à un ordi d'un membre du groupe

|          | Nom      | Adresse MAC | Adresse IP |
| -------- | -------- | ----------- | ---------- |
| Wifi     |Killer(R) Wireless-AC 1550 Wireless Network dapter (9260NGW) 160MHz| 14-4F-8A-22-6C-C1 |10.33.3.208|
| Ethernet |Realtek PCIe GBE Family Controller|B0-25-AA-2D-D1-02|169.254.178.255|

Pour connaitre l'adresse de la passerelle, toujours la même commande : 
>Adresse de passerelle : **10.33.3.253**
---
**Trouvez comment afficher les informations sur une carte IP (change selon l'OS) :**

En allant dans les configurations wifi du PC J'obtiens
![](https://i.imgur.com/Y444zMT.png)


>|          | IP | MAC | GateWay |
>| -------- | -------- | -------- | - |
>| Ce PC | 10.33.3.208 | 14-4F-8A-22-6C-C1 | 10.33.3.253 |

**A quoi sert la gateway dans le réseau d'YNOV ?**
La gateway sert à communiquer entre les réseaux.

---

### 2. Modifications des informations
#### A. Modification d'adresse IP (part 1)

![](https://i.imgur.com/LlfrAWC.png)

Après avoir changer l'adresse IP de 10.33.3.208 à 10.33.3.10, je constate que je suis toujours connecté au wifi mais que je perd internet car quelqu'un possède déjà cette IP. Pour régler ce problème je vais aller chercher les adresses libre grâce au logiciel NMAP.

---
#### B. nmap

Commande :
>nmap -sn -PE 10.33.0.0/22

```
PS C:\Users\tauri> nmap -sn -PE 10.33.0.0/22
Starting Nmap 7.80 ( https://nmap.org ) at 2020-01-23 09:33 Paris, Madrid
Nmap scan report for 10.33.0.10
Host is up (0.010s latency).
MAC Address: AC:ED:5C:72:CD:BF (Intel Corporate)
Nmap scan report for 10.33.0.14
Host is up (0.060s latency).
MAC Address: 28:16:AD:AE:2A:2C (Intel Corporate)
Nmap scan report for 10.33.0.16
Host is up (1.4s latency).
MAC Address: 74:EB:80:E6:EB:AA (Samsung Electronics)
Nmap scan report for 10.33.0.17
Host is up (0.0080s latency).
MAC Address: 7C:76:35:E5:3F:53 (Intel Corporate)
Nmap scan report for 10.33.0.22
Host is up (0.0080s latency).
{...}
MAC Address: 1C:CC:D6:8F:D6:13 (Unknown)
Nmap scan report for 10.33.3.248
Host is up (0.62s latency).
MAC Address: 80:0C:67:4F:FF:10 (Unknown)
Nmap scan report for 10.33.3.253
Host is up (0.0040s latency).
MAC Address: 00:12:00:40:4C:BF (Cisco Systems)
Nmap scan report for 10.33.3.254
Host is up (0.0040s latency).
MAC Address: 94:0C:6D:84:50:C8 (Tp-link Technologies)
Nmap scan report for 10.33.3.123
Host is up.
Nmap done: 1024 IP addresses (347 hosts up) scanned in 51.24 seconds
```
---
#### C.  Modification d'adresse IP (part 2)

En regardant ce que donne la commande, je constate que l'ip 10.33.3.249 est possiblement disponible. Je test de modifier mon IP avec et je perd internet. Il s'agit surement d'un windows qui ne s'affiche pas dans la liste.

---
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




---
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