# BERGEZCASALOU TP2
## Compte rendu TP2

### I. Création et utilisation simple d'une machine CentOS

#### 4. Configuration réseau d'une machine CentOS

|Name|IP|MAC|Fonction|
|----|--|---|--------|
|enp0s3|10.0.2.15/24|08:00:27:7d:46:30|carte NAT|
|enp0s8|10.2.1.2/24|08:00:27:85:f0:96|Carte ethernet|

- Changer vers une autre IP statique
On entre la commande ```/etc/sysconfig/network-scripts```,
puis ```sudo nano ifcfg-enp0s8```

- Preuve du changement

|Name|IP|MAC|Fonction|
|----|--|---|--------|
|enp0s3|10.0.2.15/24|08:00:27:7d:46:30|carte NAT|
|enp0s8|10.2.1.3/24|08:00:27:85:f0:96|Carte ethernet|

```
ping 10.2.1.3

Envoi d’une requête 'Ping'  10.2.1.3 avec 32 octets de données :
Réponse de 10.2.1.3 : octets=32 temps<1ms TTL=64
Réponse de 10.2.1.3 : octets=32 temps<1ms TTL=64
Réponse de 10.2.1.3 : octets=32 temps<1ms TTL=64
Réponse de 10.2.1.3 : octets=32 temps<1ms TTL=64

Statistiques Ping pour 10.2.1.3:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```

#### 5.

- Scan nmap du réseau Host-Only

![](https://i.imgur.com/Vm1onrd.png)

- ss pour lister les ports TCP et UDP en écoute sur la machine

![](https://i.imgur.com/WQm6OAY.png)

par exemple sur le port 22, le programme "sshd" est en écoute

### II. Notion de ports

#### 1. SSH

- Pour l'instant ssh écoute sur le port 22 et sur toutes les IP


- La connection ssh de l'hote vers la vm, on verifie la connection avec ```ss```
![](https://i.imgur.com/AJflEVK.png)

#### 2. Firewall
##### A. SSH

- on modifie /etc/ssh/sshd_config
![](https://i.imgur.com/G6QRZOW.png)

- le service ssh ecoute sur le nouveau port 1111
![](https://i.imgur.com/B6rTPhQ.png)

##### B. Netcat

- on cree un chat
![](https://i.imgur.com/ov385xb.png)

- on cree plusieurs chat
![](https://i.imgur.com/gXF7pxP.png)


et on inverse
![](https://i.imgur.com/bOIrCcH.png)

#### 3. Wireshark

![](https://i.imgur.com/fo5BAvY.png)
![](https://i.imgur.com/XlHOZYW.png)

SYNC --> demande de synchronisation
SYNC, ACK --> demande d'acceptation
ACK --> acceptation

### III. Routage statique

#### 2. Configuration du routage

##### A. PC1 = Thomas AURIOL

Ping du PC1 vers le host-only du PC2

![](https://i.imgur.com/tLMftiA.png)

##### B. PC2 = Malory BERGEZ-CASALOU

Ping du PC2 vers le host-only du PC1

![](https://i.imgur.com/6XBZpXX.png)

##### C. WM2 = Malory BERGEZ-CASALOU

Ping de la VM2 vers le PC1

![](https://i.imgur.com/5TxDn3w.png)

##### D. EL GRAN FINAL!!!!!!

Ping de la VM1 vers la VM2

![](https://i.imgur.com/y3YDR0a.png)

#### 3. Configuration des noms de domaine

Ping de la VM1 vers la VM2 en ayant configuré les noms de domaines : 

![](https://i.imgur.com/vZuC5K9.png)

Traceroot :

En executant sur la VM1 la commande :
> traceroot vm2.tp2.b1

On se rend compte que cele n'affiche rien.

Après avoir réinstallé traceroot sur la VM1, on a réexécuté la commande et le même problême persiste.

Ouverture de netcat sur la VM1 : 

![](https://i.imgur.com/ZSZXMOA.png)

Connexion client de la VM2 sur le netcat de la VM1 avec des messages de test : 

![](https://i.imgur.com/ClYQ7gC.png)
