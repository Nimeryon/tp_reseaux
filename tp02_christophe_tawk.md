# TP-2 - Machine virtuelle, réseau, serveurs, routage simple CHRISTOPHE TAWK

## I. Création et utilisation simples d'une VM CentOS

### 1. Configuration réseau d'une machine CentOS

|Name|IP|MAC|Fonction|
|----|--|---|-----------|
|enp0s3|10.0.2.15/24|08:00:27:fd:59:a4|Carte NAT (accès internet)|
|enp0s8|10.2.1.2/24|08:00:27:bc:7a:dc|Carte ethernet (connexion host-only)|

#### Changer vers une autre IP statique

Pour cela on va dans : 
```
/etc/sysconfig/network-scripts
````
Et on modifie le fichier avec la commande : 
```
<sudo nano ifcfg-enp0s8>
```
Ça donne : 

![](https://i.imgur.com/4pMj7CP.png)

On verifie avec un ip a:

![](https://i.imgur.com/RAIAlqe.png)

On constate effectivement que l'adresse IP a été changé.

Voici le ping du PC hôte dans le réseau Host-Only : 

![](https://i.imgur.com/PbrS2uW.png)

### 2. Appréhension de quelques commandes

#### nmap
scan nmap du réseau Host-Only : 

![](https://i.imgur.com/MgCSobO.png)

les adresses ip trouvées sont : 

10.2.1.1, et 10.2.1.3.

![](https://i.imgur.com/GEi2TIl.png)
Sur le PC Host, et on peux constater que l'on retrouve bien les adresses IP trouvé avec nmap.

#### ss

![](https://i.imgur.com/SjCt619.png)

Avec la commande `sudo ss -l -t -u -n -p`, on peut trouver quel programme ecoute sur quel port.

![](https://i.imgur.com/KKqUD7D.png)

Par exemple, sur le port 25 en tcp, le programme "dhclient" est en écoute pour l'ip 127.0.0.1.

## II. Notion de ports

### 1. SSH

Pour voir quel port ecoute quelle IP, il faut utiliser netstat. Pour cela, il faut verifier si netstat est installé. Sur notre vm, netstat n'est pas installé. Il faut donc l'installer en entrant la commande : 

``` sudo yum -y install net-tools ```

![](https://i.imgur.com/bPc9xwX.png)

Grâce à la commande ``` sudo netstat -tnplv | grep ssh ```, on peut constater que sur le port 22 à l'ip 0.0.0.0, le port ssh est en écoute.

Pour se connecter en ssh avec la machine hote il faut installer "OpenSSH Server Software Package".
Pour ce faire , il faut entrer la commande : 

``` sudo yum –y install openssh-server openssh-clients```


On active dans le systeme, l'ecoute ssh avec la commande :

````
sudo systemgctl start sshd
````

puis on verifie que le systeme est bien actif:

![](https://i.imgur.com/nDyb9sM.png)

Ensuite, sur un terminal du pc hote, on active la connexion ssh avec la vm centos : 

![](https://i.imgur.com/8O3F6Ey.png)

Pour verifier que la connexion ssh est active on entre la commande ss, et ça donne :

![](https://i.imgur.com/0uXaPGB.png)

### 2. Firewall

#### A. SSH

Tout d'abord on accede au fichier où l'on doit modifier le port. Ensuite, on modifie le port avec une valeur entre 1024 et 65535 : 

![](https://i.imgur.com/sE5HZfd.png)

On enlève le "#", car cela correspond à un commentaire.

Puis, on restart le systeme avec la commande, 
````
sudo systemctl restart sshd
````
et après on verifie que le systeme ecoute bien au nouveau port avec la commande 
````
sudo systemctl status sshd
````

![](https://i.imgur.com/xuIE36y.png)

On constate effectivement que le systeme ecoute bien au nouveau port entré.

On constate aussi sur l'hote que le systeme est bien connecté:

![](https://i.imgur.com/cyL5uCf.png)

On a un message "connection timed out", la connexion a echoué a cause du Firewall.

Donc on va chercher a ouvrir le port correspondant au firewall.
Pour cela, on utilise la commande 
````
firewall-cmd
````
On va autoriser le firewall a ecouter au port voulu : 

![](https://i.imgur.com/H5daAi7.png)

Puis avec un reload, on confirme les modifications apportées.

#### B. Netcat

On cherche a créer un chat privé. Pour cela, on va connecter la vm avec le pc hôte. 
On ouvre d'abord un nouveau port comme fait precédemment, mais cette fois ci, en 1112.
Ensuite, on connecte en ssh la vm avec le pc hote sur un premier terminal, puis on entre la commande ````nc -l -p 1112 ````. Cette commande va entrer en connexion avec le terminal du pc hôte.
Sur un deuxieme terminal, on connecte le pc hôte avec la vm, en utilisant la commande ````ncat 10.2.1.3 ````.
On a donc un chat privé entre les 2 terminaux.
Ensuite, dans un troisieme terminal on entre la commande ````ss````. On peut bien voir le connexion avec netcat entre la vm et le pc hote :

![](https://i.imgur.com/vXgyXeq.png)

### 3. Wireshark

![](https://i.imgur.com/waGnGiE.png)

On peut constater trois messages :

[SYNC] --> Demande de synchronisation
[SYNC, ACK] --> Demande d'acceptation
[ACK] --> Validation de l'acceptation


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
