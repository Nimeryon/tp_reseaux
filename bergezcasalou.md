# BERGEZCASALOU
## Compte rendu TP1
---
### I 
#### 1

Pour afficher les informations de la machine : ipconfig /all

- carte wifi
-- nom : Intel(R) Wireless-AC 9560
-- adresse mac : 30-24-32-9E-73-20
-- adresse ip : 10.33.0.24
-- adresse réseau : 10.33.0.0
-- adresse de broadcast : 10.33.255.255

- carte ethernet : statut déconnecté
-- nom : Killer E2400 Gig abit Ethernet Controller
-- adresse mac : 30-9C-23-93-94-E1
-- adresse ip : (déconnecté donc aucune)

- gateway
-- passerelle par défaut : 10.33.3.253

- En GUI
![](https://i.imgur.com/6P6cGkF.png)

-- ip : 10.33.0.24
-- mac : 30-24-32-9E-73-20
-- gateway : 10.33.3.253

- Gateway du réseau ynov
-- Elle sert à pouvoir communiquer entre les réseaux

#### 2 
##### A
![](https://i.imgur.com/rxt37uD.png)

##### B

![](https://i.imgur.com/SR3RbgD.png)

![](https://i.imgur.com/1VOHVpI.png)

##### C
Commande pour observer les adresses disponibles :
![](https://i.imgur.com/5hTdmAa.png)

J'ai plus qu'à choisir une adresse disponible :
![](https://i.imgur.com/ATHhZ6i.png)

Changement du gateway :
![](https://i.imgur.com/rxt37uD.png)

---
### II




#### 3

Adresse IP de Thomas : 192.168.69.194

![](https://i.imgur.com/ilyEvEn.png)

Adresse Ip de Malory : 192.168.69.193

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

#### 4

C'est moi qui va envoyer le WI-FI à Thomas.

Pour tester la connectivité a Internet, Thomas a entré la commande 'curl google.fr'.
Résultat : 

![](https://i.imgur.com/G8KzmPK.png)

Pour bien voir que les requêtes passent par la passerelle choisie, on fait : 

![](https://i.imgur.com/9yidNOW.png)

#### 5
Moi (PC Serveur):
![](https://i.imgur.com/38EAKho.png)

Thomas (PC Client):
![](https://i.imgur.com/CtK1Eut.png)
#### 6 wireshark
ping 192.168.137.2
En recevant un ping on obtient cela
![](https://i.imgur.com/XJ6ij9H.png)

./nc64.exe 192.168.137.2 -u 8888
lorsque je reçois des paquets netcat
![](https://i.imgur.com/MFut4r5.png)


#### 7 firewall
Nous avons autorisé les parametres de type echo ICMP type  en entrée en type 0 en sortie
![](https://i.imgur.com/FpEH7VT.png)
et fonctionne avec le firewall activé 
![](https://i.imgur.com/jCsL7gV.png)





---
### III

#### 1

ipconfig /all
![](https://i.imgur.com/CjhAtYQ.png)

adresse DHCP: 10.33.51.254
adresse d'expiration du bail: mercredi 29 janvier 2020 13:47:50

ipconfig /renew
![](https://i.imgur.com/g3ph3D5.png)
nouvelle ip après renouvellement de bail: 10.33.48.255

#### 2

adresse ip du dns que connait mon ordinateur
![](https://i.imgur.com/wsYGbwb.png)

nslookup
![](https://i.imgur.com/fSDFvAu.png)
Pour faire des requetes DNS vers des adresses IP, on fait : 

![](https://i.imgur.com/jy2Lh7b.png)

Ici, avec ces commandes, l'ordinateur ne reconnait pas les adresses IP entrées.
Cependant, avec certains sites, on peut trouver à quels sites appartiennent ces IP.

