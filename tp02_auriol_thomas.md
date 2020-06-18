# TP 2 - Machine virtuelle, réseau, serveurs, routage simple
## I. Création et utilisation simples d'une VM CentOS
### 4. Configuration réseau d'une machine CentOS

| Name     | Ip       | Mac      | Fonction |
| -------- | -------- | -------- | -------- |
| enp0s3   | 10.0.2.15/24 | 08:00:27:62:8e:83 | Carte NAT (accès internet) |
| enp0s8   | 10.2.1.2/24 | 08:00:27:74:09:80 | Carte ethernet (connexion host only) |

Aprés avoir changé l'ip static en modifiant le fichier ifcfg-enp0s8. En faisant la commande ip a je vois bien que mon ip est passé à : 

10.2.1.3/24

avec un ping depuis le pc host : 

![](https://i.imgur.com/kQfpv0f.png)

### 5. Appréhension de quelques commandes
#### Nmap :

Avec nmap sur la vm : 

![](https://i.imgur.com/FKPElEJ.png)

Avec nmap sur l'host : 

![](https://i.imgur.com/Zhu8jBH.png)

#### ss : 

![](https://i.imgur.com/loii6Bo.png)

les programmes écoutant sur chacun de ces ports : 

>udp dhclient port 28
>tcp master port 25 pour l'ip 127.0.0.1
>tcp master port 25 pour toutes les ips
>tcp sshd port 22 pour toutes les ips

## II. Notion de ports
### 1. SSH

En ouvrant un serveur ssh avec la commande : 
> systemctl start sshd

Je regarde sons statut avec : 
> systemctl status sshd

j'obtiens les infos suivantes : 
![](https://i.imgur.com/Fmqnxeg.png)

je peut voir que le serveur SSH écoute l'ip 0.0.0.0 port 22 et toutes les ips port 22.

---

Pour me connecter depuis mon host au serveur ssh de la vm j'utilise la commande suivante : 
> ssh -p 22 root@10.2.1.3

-p pour le port
root le nom d'utilisateur
suivi d'un @ et l'adresse ip.

avec la commande ss je vois bien que je suis connecté : 
![](https://i.imgur.com/8SLkTTg.png)

### 2. Firewall
#### A. SSH

Je change dans le fichier /etc/ssh/ssh-config la ligne : 
> #Port 22

qui est un commentaire, en : 
> Port 12345

je restart le serveur ssh avec systemctl restart sshd

et je vérifie que le port d'écoute est bien 12345 en regardant le status : 
![](https://i.imgur.com/4F2bpYn.png)

en testant la connexion sur l'host avec le nouveau port 12345, j'obtiens bien un time out.

J'ajoute le port 12345 dans le firewall de la vm grâce à la commande : 
> firewall-cmd --add-port=12345/tcp --permanent
> firewall-cmd --reload

Je retest la connexion depuis le pc host et je constate que tout fonctionne normalement.

#### B. Netcat

J'ouvre le serveur ssh sur la vm.
Sur l'host j'ouvre 3 powershell 2 avec la connexion ssh et un normal, sur l'un des deux connecté en ssh j'ouvre un netcat sur le port 12346 préalablement autorisé sur le firewall.
> nc -l -p 12346

sur le powershell normal je me connecte au netcat : 
> ncat 10.2.1.3 12346

J'envoie un message d'un côté et je le reçoit bien de l'autre.

sur le deuxième powershell connecté en ssh j'execute un ss : 
![](https://i.imgur.com/vUDObjk.png)

---

Je change de serveur/client, je termine les connexions netcat active.

Sur le powershell j'active le netcat : 
> ncat -l -p 12346

j'autorise le port côté windows

Sur le premier powershell ssh je me connecte sur le netcat :
> nc 10.2.1.1 12346

### 3. Wireshark

Pendant que l'host fait le serveur : 
J'envoie coucou depuis le serveur, dans wireshark je vois ce package : 
![](https://i.imgur.com/lsjgHsg.png)

Lorsque le client ce connecte on voit 3 messages sur wireshark.
![](https://i.imgur.com/MKZW7o7.png)

> 1 : Demande de synchronisation
> 2 : Acceptation de synchronisation
> 3 : Validation de l'acceptation

## III. Routage statique

### 2. Configuration du routage

#### A. PC1 = Thomas AURIOL

Ping du PC1 vers le host-only du PC2

![](https://i.imgur.com/tLMftiA.png)

#### B. PC2 = Malory BERGEZ-CASALOU

Ping du PC2 vers le host-only du PC1

![](https://i.imgur.com/6XBZpXX.png)

#### C. WM2 = Malory BERGEZ-CASALOU

Ping de la VM2 vers le PC1

![](https://i.imgur.com/5TxDn3w.png)

#### D. EL GRAN FINAL!!!!!!

Ping de la VM1 vers la VM2

![](https://i.imgur.com/y3YDR0a.png)

### 3. Configuration des noms de domaine

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

