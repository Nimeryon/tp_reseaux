# TP 5 - Une "vraie" topologie ?
## I. Toplogie 1 - intro VLAN

![](https://i.imgur.com/xCveYmu.png)

### 2. Setup clients

**Ping :**
>- **Admin1 vers Admin2**
>**ping 10.5.10.12**
```
>84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.285 ms
>84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.630 ms
>84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.484 ms
>84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.404 ms
>84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.421 ms
```

>- **Ping1 vers Ping2**
>**ping 10.5.20.12**
```
>84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.347 ms
>84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.417 ms
>84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.396 ms
>84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.478 ms
>84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.463 ms
```

---
### 3. Setup VLANs

>**Switch 1:**
>**show vlan**
```
>VLAN Name                             Status    Ports
>10   admins                           active
>20   guests                           active
```

>**show interfaces trunk**
```
>Port        Vlans allowed and active in management domain
>Et0/0       1,10,20
```

>**Switch 2 :**
>**show vlan**
```
>VLAN Name                             Status    Ports
>10   admins                           active
>20   guests                           active
```

>**show interfaces trunk**
```
>Port        Vlans allowed and active in management domain
>Et0/0       1,10,20
```

**Ping :**
>- **Admin1 vers Admin2**
>**ping 10.5.10.12**
```
>84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.304 ms
>84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.377 ms
>84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.435 ms
>84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.390 ms
>84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.402 ms
```

>- **Ping1 vers Ping2**
>**ping 10.5.20.12**
```
>84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.261 ms
>84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.494 ms
>84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.509 ms
>84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.435 ms
>84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.608 ms
```

On essaie de faire passer Guest 1 dans le réseau admin et on vois si on peut toujours ping un admin.
>**ip 10.5.10.13/24 10.5.10.254**
>**Ping Guest1 vers Admin1**

>**ping 10.5.10.11**
```
host (10.5.10.11) not reachable
```

---

## II. Topologie 2 - VLAN, sous-interface, NAT
### 1. Mise en place de la topologie

![](https://i.imgur.com/UqUyprk.png)

#### 2. Adressage

**Ping :**
>- **Admin1 vers Admin2**
>**ping 10.5.10.12**
```
>84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.316 ms
>84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.401 ms
```

>- **Admin2 vers Admin3**
>**ping 10.5.10.13**
```
>84 bytes from 10.5.10.13 icmp_seq=1 ttl=64 time=0.279 ms
>84 bytes from 10.5.10.13 icmp_seq=2 ttl=64 time=0.396 ms
```

>- **Admin3 vers Admin1**
>**ping 10.5.10.11**
```
>84 bytes from 10.5.10.11 icmp_seq=1 ttl=64 time=0.399 ms
>84 bytes from 10.5.10.11 icmp_seq=2 ttl=64 time=0.518 ms
```

>- **Guest1 vers Guest2**
>**ping 10.5.20.12**
```
>84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.262 ms
>84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.409 ms
```

>- **Guest2 vers Guest3**
>**ping 10.5.20.13**
```
>84 bytes from 10.5.20.13 icmp_seq=1 ttl=64 time=0.251 ms
>84 bytes from 10.5.20.13 icmp_seq=2 ttl=64 time=0.426 ms
```

>- **Guest3 vers Guest1**
>**ping 10.5.20.11**
```
>84 bytes from 10.5.20.11 icmp_seq=1 ttl=64 time=0.392 ms
>84 bytes from 10.5.20.11 icmp_seq=2 ttl=64 time=0.543 ms
```

---

#### 3. VLAN

>- **Switch 1:**
>**show vlan**
```
>VLAN Name                             Status    Ports
>10   admins                           active
>20   guests                           active
```
>**show interfaces trunk**
```
>Port        Vlans allowed and active in management domain
>Et0/0       1,10,20
```

>- **Switch 2 :**
>**show vlan**
```
>VLAN Name                             Status    Ports
>10   admins                           active
>20   guests                           active
```
>**show interfaces trunk**
```
>Port        Vlans allowed and active in management domain
>Et0/1       1,10,20
>Et0/2       1,10,20
```


>- **Switch 3 :**
>**show vlan**
```
>VLAN Name                             Status    Ports
>10   admins                           active
>20   guests                           active
```
>**show interfaces trunk**
```
>Port        Vlans allowed and active in management domain
>Et0/0       1,10,20
```

On essaie encore une fois de faire passer Guest 1 dans le réseau admin et on vois si on peut toujours ping un admin.
>**ip 10.5.10.14/24 10.5.10.254**
>**Ping Guest1 vers Admin3**

>**ping 10.5.10.13**
```
>host (10.5.10.13) not reachable
```

---

#### 4. Sous-interfaces

Pour configurer les sous-interfaces de mon routeur, suffit de suivre ton petit encadré et ne pas oublié de copier la config dans le demarrage.

**Ping :**
>- **Admin1 vers routeur**
>**ping 10.5.10.254**
```
84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=6.942 ms
84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=8.045 ms
```

>- **Admin2 vers routeur**
>**ping 10.5.10.254**
```
>84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=10.354 ms
>84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=10.681 ms
```

>- **Admin3 vers routeur**
>**ping 10.5.10.254**
```
>84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=10.341 ms
>84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=11.936 ms
```

>- **Guest1 vers routeur**
>**ping 10.5.20.254**
```
>84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=10.019 ms
>84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=3.877 ms
```


>- **Guest2 vers routeur**
>**ping 10.5.20.254**
```
>84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=10.306 ms
>84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=1.210 ms
```

>- **Guest3 vers routeur**
>**ping 10.5.20.254**
```
>84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=10.279 ms
>84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=3.016 ms
```

---

#### 5. NAT

Interface 1/0 passé en outside
Sous interface 0/0.10 - 0/0.20 passé en inside

Configuration du nat sur l'interface 1/0

**Ping :**
>- **Admin1 vers 8.8.8.8**
>**ping 8.8.8.8**
```
>*10.5.10.254 icmp_seq=1 ttl=255 time=10.005 ms (ICMP type:3, code:1, Destination host unreachable)
>*10.5.10.254 icmp_seq=2 ttl=255 time=6.607 ms (ICMP type:3, code:1, Destination host unreachable)
```

>- **Guest2 vers 8.8.8.8**
>**ping 8.8.8.8**
```
>*10.5.20.254 icmp_seq=1 ttl=255 time=10.195 ms (ICMP type:3, code:1, Destination host unreachable)
>*10.5.20.254 icmp_seq=2 ttl=255 time=7.385 ms (ICMP type:3, code:1, Destination host unreachable)
```

---

## III. Topologie 3 - Ajouter des services
### 1. Mise en place de la topologie

![](https://i.imgur.com/0sHJvIY.png)

### 2. Basic setup

Tout le monde peut se ping, mais j'ai un problême de NAT qui m'empèche d'acceder internet

### 3. Serveur DHCP

Le problême de NAT m'enpêche d'installer les paquets...

### 4. Serveur Web

La même içi...

### 5. Serveur DNS

içi aussi...

...

