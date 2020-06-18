# TP 5 - Une "vraie" topologie ?

## I. Toplogie 1 - intro VLAN

### 1. Mise en place de la topologie

On va faire les configuartions sur les clients.

On va entrer la commande ip <IP_ADRESS>/<NET_MASK> <GATE_WAY>. On le fait sur les 4 VPCS : 

-Sur admin1 
```
PC1> ip 10.5.10.11/24 10.5.10.254
Checking for duplicate address...
PC1 : 10.5.10.11 255.255.255.0 gateway 10.5.10.254
```

-Sur guest1
```
guest1> ip 10.5.20.11/24 10.5.20.254
Checking for duplicate address...
PC1 : 10.5.20.11 255.255.255.0 gateway 10.5.20.254
```

-Sur admin2
```
PC3> ip 10.5.10.12/24 10.5.10.254
Checking for duplicate address...
PC1 : 10.5.10.12 255.255.255.0 gateway 10.5.10.254
```

-Sur guest2
```
guest2> ip 10.5.20.12/24 10.5.20.254
Checking for duplicate address...
PC1 : 10.5.20.12 255.255.255.0 gateway 10.5.20.254
```

### 2. Setup clients

Maintenant, on va essayer de ping les vms entre elles.

Ping de admin2 vers admin1 : 
```
PC3> ping 10.5.10.11
84 bytes from 10.5.10.11 icmp_seq=1 ttl=64 time=0.330 ms
84 bytes from 10.5.10.11 icmp_seq=2 ttl=64 time=0.482 ms
84 bytes from 10.5.10.11 icmp_seq=3 ttl=64 time=0.468 ms
84 bytes from 10.5.10.11 icmp_seq=4 ttl=64 time=0.453 ms
84 bytes from 10.5.10.11 icmp_seq=5 ttl=64 time=0.436 ms
```

Ping de guest1 vers guest2 : 
```
guest1> ping 10.5.20.12
84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.472 ms
84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.472 ms
84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.495 ms
84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.456 ms
84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.548 ms
```

### 3. Setup VLANs

On va donner un nom au vlan voulu, sur les 2 switches.

Sur le switch1 : 
```
IOU1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU1(config)#vlan 10
IOU1(config-vlan)#name admins
```

```
IOU1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU1(config)#vlan 20
IOU1(config-vlan)#name guests
```

Sur le switch2 : 
```
IOU2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU2(config)#vlan 10
IOU2(config-vlan)#name admins
IOU2(config-vlan)#exit
```

```
IOU2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU2(config)#vlan 20
IOU2(config-vlan)#name guests

```

Maintenant, on va faire les configurations. On veut que les switchs aient des ports en acces vers les clients, et que les ports reliants les switches soient en trunk. On fait donc comme ceci : 

Sur le switch1 : 
```
IOU1(config)#interface Ethernet 0/1
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport acces vlan 10
IOU1(config-if)#exit
IOU1(config)#interface Ethernet 0/2
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport acces vlan 20
IOU1(config-if)#exit
IOU1(config)#interface Ethernet 0/0
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switchport trunk allowed vlan add 10,20
IOU1(config-if)#exit
IOU1(config)#exit

```

Sur le switch2 : 
```
IOU2(config)#interface Ethernet 0/1
IOU2(config-if)#switchport mode access
IOU2(config-if)#switchport access vlan 10
IOU2(config-if)#exit
IOU2(config)#interface Ethernet 0/2
IOU2(config-if)#switchport mode access
IOU2(config-if)#switchport access vlan 20
IOU2(config-if)#exit
IOU2(config)#interface Ethernet 0/0
IOU2(config-if)#switchport trunk encapsulation dot1q
IOU2(config-if)#switchport mode trunk
IOU2(config-if)#switchport trunk allowed vlan add 10,20
IOU2(config-if)#exit
```

on verifie si les switch sont bien en trunk : 

Sur le switch1 : 
```
IOU1#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20
```

Sur le switch2 : 
```
IOU2#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20
```

Et que les ports vers les clients soient en acces : 

Sur le switch1 : 
```
IOU1#show vlan
VLAN Name                             Status    

10   admins                           active    Et0/1
20   guests                           active    Et0/2
```

Sur le switch2 : 
```
IOU2#show vlan
VLAN Name                             Status    

10   admins                           active    Et0/1
20   guests                           active    Et0/2 
```

Maintenant qu'on a tout configuré, on vérifie que les pings marchent toujours.

De admin1 vers admin2 : 
```
PC1> ping 10.5.10.12
84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.299 ms
84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.436 ms
84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.413 ms
84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.474 ms
84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.560 ms
```

De guest1 vers guest2 : 
```
guest1> ping 10.5.20.12
84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.304 ms
84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.444 ms
84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.465 ms
84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.456 ms
84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.462 ms
```

Puis, on verifie que même si l'un des guests change d'ip, il ne peut pas joindre le réseau admin.

On change l'ip de guest1 : 
```
guest1> ip 10.5.10.13
Checking for duplicate address...
PC1 : 10.5.10.13 255.255.255.0
```

On fait un ping de guest1 vers admin1 : 
```
guest1> ping 10.5.10.11
host (10.5.10.11) not reachable
```

On voit bien que même si guest change de réseau pour joindre admin, cela ne marche pas.

## II. Topologie 2 - VLAN, sous-interface, NAT

### 2. Adressage

Configuration des nouveaux clients : 

-admin3
```
PC1> ip 10.5.10.13/24 10.5.10.254
Checking for duplicate address...
PC1 : 10.5.10.13 255.255.255.0 gateway 10.5.10.254
```

-guest3

```
PC2> ip 10.5.20.13/24 10.5.20.254
Checking for duplicate address...
PC1 : 10.5.20.13 255.255.255.0 gateway 10.5.20.254
```

Maintenant on va voir si tout fonctionne. Pour ce faire on va faire pleins de ping.

Ping de guest3 vers guest1 : 
```
PC2> ping 10.5.20.11
84 bytes from 10.5.20.11 icmp_seq=1 ttl=64 time=0.532 ms
84 bytes from 10.5.20.11 icmp_seq=2 ttl=64 time=0.942 ms
84 bytes from 10.5.20.11 icmp_seq=3 ttl=64 time=0.608 ms
84 bytes from 10.5.20.11 icmp_seq=4 ttl=64 time=0.685 ms
84 bytes from 10.5.20.11 icmp_seq=5 ttl=64 time=0.603 ms
```
Ping de guest2 vers guest3 : 
```
guest2> ping 10.5.20.13
84 bytes from 10.5.20.13 icmp_seq=1 ttl=64 time=0.321 ms
84 bytes from 10.5.20.13 icmp_seq=2 ttl=64 time=0.504 ms
84 bytes from 10.5.20.13 icmp_seq=3 ttl=64 time=0.473 ms
84 bytes from 10.5.20.13 icmp_seq=4 ttl=64 time=0.451 ms
84 bytes from 10.5.20.13 icmp_seq=5 ttl=64 time=0.526 ms
```

Ping de admin3 vers admin2 : 
```
PC1> ping 10.5.10.12
84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.346 ms
84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.440 ms
84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.507 ms
84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.484 ms
84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.449 ms
```

Ping de admin1 vers admin3 : 
```
PC1> ping 10.5.10.13
84 bytes from 10.5.10.13 icmp_seq=1 ttl=64 time=0.472 ms
84 bytes from 10.5.10.13 icmp_seq=2 ttl=64 time=0.625 ms
84 bytes from 10.5.10.13 icmp_seq=3 ttl=64 time=0.586 ms
84 bytes from 10.5.10.13 icmp_seq=4 ttl=64 time=0.569 ms
84 bytes from 10.5.10.13 icmp_seq=5 ttl=64 time=0.526 ms
```

### 3. VLAN

Pour ce faire, on fait les configurations faites plus haut, sur le switch3, et on fait les modifications sur la nouvelle interface de switch2 connecté au switch3 : 

-switch3 : 
```
IOU3(config)#vlan 10
IOU3(config-vlan)#name admins
IOU3(config-vlan)#exit
IOU3(config)#vlan 20
IOU3(config-vlan)#name guests
IOU3(config-vlan)#exit
IOU3(config)#interface Ethernet 0/1
IOU3(config-if)#switchport mode acces
IOU3(config-if)#switchport acces vlan 10
IOU3(config-if)#exit
IOU3(config)#interface Ethernet 0/2
IOU3(config-if)#switchport mode acces
IOU3(config-if)#switchport acces vlan 20
IOU3(config-if)#exit
IOU3(config)#interface Ethernet 0/0
IOU3(config-if)#switchport trunk encapsulation dot1q
IOU3(config-if)#switchport mode trunk
IOU3(config-if)#switchport trunk allowed vlan add 10,20
IOU3(config-if)#exit
```

```
IOU3#show vlan

10   admins                           active    Et0/1
20   guests                           active    Et0/2
```

```
IOU3#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20
```

-switch2 : 
```
IOU2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU2(config)#interface Eth
IOU2(config)#interface Ethernet 0/3
IOU2(config-if)#switchport trunk encapsulation dot1q
IOU2(config-if)#switchport mode trunk
IOU2(config-if)#switchport trunk allowed vlan add 10,20
IOU2(config-if)#exit
```

```
IOU2#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1
Et0/3       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094
Et0/3       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20
Et0/3       1,10,20

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20
Et0/3       1,10,20
```

```
IOU2#show vlan

10   admins                           active    Et0/1
20   guests                           active    Et0/2
```

Puis, pour verifier, on va changer l'ip d'un guest pour qu'il essaye d'acceder au réseau admin, et on voit ce que ca donne.

```
PC2> ip 10.5.10.14/24
Checking for duplicate address...
PC1 : 10.5.10.14 255.255.255.0

PC2> ping 10.5.10.11
host (10.5.10.11) not reachable
```

On remarque que le guest ne peut pas rejoindre le reseau admin.

### 4. Sous-interfaces

```
R1#conf t
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip address 10.5.10.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 10.5.20.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0
R1(config-if)#no shut
R1(config-if)#exit
```

On fait un "ip show int br" pour vérifier que tout est bon.
```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  up                    up  
FastEthernet0/0.10         10.5.10.254     YES manual up                    up  
FastEthernet0/0.20         10.5.20.254     YES manual up                    up  
FastEthernet1/0            unassigned      YES unset  administratively down down
```

On oublie pas de faire les modifications sur le switch2 : 
```
IOU2(config)#interface Ethernet 1/0
IOU2(config-if)#switchport trunk encapsulation dot1q
IOU2(config-if)#switchport mode trunk
IOU2(config-if)#switchport trunk allowed vlan 10,20
```

```
IOU2#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1
Et0/3       on               802.1q         trunking      1
Et1/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094
Et0/3       1-4094
Et1/0       10,20

Port        Vlans allowed and active in management domain
Et0/0       1,10,20
Et0/3       1,10,20
Et1/0       10,20

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20
Et0/3       1,10,20
Et1/0       10,20
```


Maintenant il est l'heure des 5 millions de ping. C'est pour verifier que les clients peuvent ping leurs passerelle. Il faut avant avoir defini les gateway (je les ai defini beaucoup plus tôt par reflexe).

-Les admins : 

admin1 vers sa passerelle : 
```
PC1> ping 10.5.10.254
84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=9.892 ms
84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=6.744 ms
84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=1.640 ms
84 bytes from 10.5.10.254 icmp_seq=4 ttl=255 time=7.076 ms
84 bytes from 10.5.10.254 icmp_seq=5 ttl=255 time=10.344 ms
```

admin2 vers sa passerelle :
```
PC3> ping 10.5.10.254
84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=9.504 ms
84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=5.312 ms
84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=1.252 ms
84 bytes from 10.5.10.254 icmp_seq=4 ttl=255 time=5.394 ms
84 bytes from 10.5.10.254 icmp_seq=5 ttl=255 time=4.839 ms
```

admin3 vers sa passerelle : 
```
PC1> ping 10.5.10.254
84 bytes from 10.5.10.254 icmp_seq=1 ttl=255 time=9.438 ms
84 bytes from 10.5.10.254 icmp_seq=2 ttl=255 time=3.264 ms
84 bytes from 10.5.10.254 icmp_seq=3 ttl=255 time=8.132 ms
84 bytes from 10.5.10.254 icmp_seq=4 ttl=255 time=1.735 ms
84 bytes from 10.5.10.254 icmp_seq=5 ttl=255 time=5.159 ms
```

guest1 vers sa passerelle : 
```
guest1> ping 10.5.20.254
84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=9.667 ms
84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=5.959 ms
84 bytes from 10.5.20.254 icmp_seq=3 ttl=255 time=3.035 ms
84 bytes from 10.5.20.254 icmp_seq=4 ttl=255 time=8.889 ms
84 bytes from 10.5.20.254 icmp_seq=5 ttl=255 time=3.882 ms
```

guest2 vers sa passerelle : 
```
guest2> ping 10.5.20.254
84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=9.144 ms
84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=9.401 ms
84 bytes from 10.5.20.254 icmp_seq=3 ttl=255 time=1.554 ms
84 bytes from 10.5.20.254 icmp_seq=4 ttl=255 time=7.721 ms
84 bytes from 10.5.20.254 icmp_seq=5 ttl=255 time=1.380 ms
```

guest3 vers sa passerelle : 
```
PC2> ping 10.5.20.254
84 bytes from 10.5.20.254 icmp_seq=1 ttl=255 time=9.503 ms
84 bytes from 10.5.20.254 icmp_seq=2 ttl=255 time=9.109 ms
84 bytes from 10.5.20.254 icmp_seq=3 ttl=255 time=6.374 ms
84 bytes from 10.5.20.254 icmp_seq=4 ttl=255 time=10.802 ms
84 bytes from 10.5.20.254 icmp_seq=5 ttl=255 time=8.081 ms
```

### 5. NAT

On va configurer le NAT. Pour ce faire, on va re referer au tp4 et faire la même chose.
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#ip nat inside
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.20
R1(config-subif)#ip nat inside
R1(config-subif)#exit
R1(config)#interface fastEthernet 1/0
R1(config-if)#ip nat outside
R1(config-if)#exit
R1(config)#access-list 1 permit any
R1(config)#ip nat inside source list 1 interface fastE
R1(config)#ip nat inside source list 1 interface fastEthernet 1/0 overload
R1(config)#exit
```

-verification
```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  up                    up  
FastEthernet0/0.10         10.5.10.254     YES manual up                    up  
FastEthernet0/0.20         10.5.20.254     YES manual up                    up  
FastEthernet1/0            unassigned      YES unset  administratively down down
NVI0                       unassigned      NO  unset  up                    up  

```

Ping vers google : 

De admin1 : 
```
PC1> ping 8.8.8.8
*10.5.10.254 icmp_seq=1 ttl=255 time=12.969 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.10.254 icmp_seq=2 ttl=255 time=9.094 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.10.254 icmp_seq=3 ttl=255 time=7.418 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.10.254 icmp_seq=4 ttl=255 time=7.619 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.10.254 icmp_seq=5 ttl=255 time=8.081 ms (ICMP type:3, code:1, Destination host unreachable)
```

De guest1 : 
```
guest1> ping 8.8.8.8
*10.5.20.254 icmp_seq=1 ttl=255 time=9.416 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.20.254 icmp_seq=2 ttl=255 time=7.912 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.20.254 icmp_seq=3 ttl=255 time=11.052 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.20.254 icmp_seq=4 ttl=255 time=10.345 ms (ICMP type:3, code:1, Destination host unreachable)
*10.5.20.254 icmp_seq=5 ttl=255 time=8.206 ms (ICMP type:3, code:1, Destination host unreachable)
```

Ca marche super bien :) 

## III. Topologie 3 - Ajouter des services

### 3. Serveur DHCP

Je ne peux plus avancer sur le tp. Mon ordinateur s'eteint des que je lance une nouvelle vm (dhcp, dns ou web).