# BERGEZ-CASALOU TP 5 - Une "vraie" topologie ?
## I. Toplogie 1 - intro VLAN
### 2. Setup clients

- ping entre admins

```bash
admin1> ping 10.5.10.12
84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.393 ms
84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.405 ms
84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.424 ms
84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.396 ms
84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.392 ms
```

```bash
admin2> ping 10.5.10.11
84 bytes from 10.5.10.11 icmp_seq=1 ttl=64 time=0.378 ms
84 bytes from 10.5.10.11 icmp_seq=2 ttl=64 time=0.423 ms
84 bytes from 10.5.10.11 icmp_seq=3 ttl=64 time=0.398 ms
84 bytes from 10.5.10.11 icmp_seq=4 ttl=64 time=0.458 ms
84 bytes from 10.5.10.11 icmp_seq=5 ttl=64 time=0.387 ms
```

- ping entre guests

```bash
guest1> ping 10.5.20.12
84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.391 ms
84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.476 ms
84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.383 ms
84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.411 ms
84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.368 ms
```

```bash
guest2> ping 10.5.20.11
84 bytes from 10.5.20.11 icmp_seq=1 ttl=64 time=0.388 ms
84 bytes from 10.5.20.11 icmp_seq=2 ttl=64 time=0.392 ms
84 bytes from 10.5.20.11 icmp_seq=3 ttl=64 time=0.398 ms
84 bytes from 10.5.20.11 icmp_seq=4 ttl=64 time=0.389 ms
84 bytes from 10.5.20.11 icmp_seq=5 ttl=64 time=0.440 ms
```

### 3. Setup VLANs

```bash
IOU1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/2, Et0/3, Et1/0, Et1/1
                                                Et1/2, Et1/3, Et2/0, Et2/1
                                                Et2/2, Et2/3, Et3/0, Et3/1
                                                Et3/2, Et3/3
10   admins                           active    Et0/1
20   guests                           active
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0
1003 tr    101003     1500  -      -      -        -    -        0      0
1004 fdnet 101004     1500  -      -      -        ieee -        0      0
1005 trnet 101005     1500  -      -      -        ibm  -        0      0


Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
```

```bash
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

- ping

```bash
admin1> ping 10.5.10.12
84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.358 ms
84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.464 ms
84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.505 ms
84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.475 ms
84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.445 ms
```

```bash
admin2> ping 10.5.10.11
84 bytes from 10.5.10.11 icmp_seq=1 ttl=64 time=0.432 ms
84 bytes from 10.5.10.11 icmp_seq=2 ttl=64 time=0.411 ms
84 bytes from 10.5.10.11 icmp_seq=3 ttl=64 time=0.384 ms
84 bytes from 10.5.10.11 icmp_seq=4 ttl=64 time=0.415 ms
84 bytes from 10.5.10.11 icmp_seq=5 ttl=64 time=0.545 ms
```

```bash
guest1> ping 10.5.20.12
84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.273 ms
84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.413 ms
84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.463 ms
84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.450 ms
84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.411 ms
```

```bash
guest2> ping 10.5.20.11
84 bytes from 10.5.20.11 icmp_seq=1 ttl=64 time=0.399 ms
84 bytes from 10.5.20.11 icmp_seq=2 ttl=64 time=0.446 ms
84 bytes from 10.5.20.11 icmp_seq=3 ttl=64 time=0.377 ms
84 bytes from 10.5.20.11 icmp_seq=4 ttl=64 time=0.422 ms
84 bytes from 10.5.20.11 icmp_seq=5 ttl=64 time=0.400 ms
```
- les guests ne peuvent pas joindre les admins

```bash
guest1> ping 10.5.10.12
host (10.5.20.254) not reachable

guest1> ping 10.5.10.11
host (10.5.20.254) not reachable
```

## II. Topologie 2 - VLAN, sous-interface, NAT

### 2. Adressage

- ping admins entre eux

```bash
admin1> ping 10.5.10.12
84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.271 ms
84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.507 ms
84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.450 ms
84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.393 ms
84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.914 ms

admin1> ping 10.5.10.13
84 bytes from 10.5.10.13 icmp_seq=1 ttl=64 time=0.562 ms
84 bytes from 10.5.10.13 icmp_seq=2 ttl=64 time=0.541 ms
84 bytes from 10.5.10.13 icmp_seq=3 ttl=64 time=0.499 ms
84 bytes from 10.5.10.13 icmp_seq=4 ttl=64 time=0.670 ms
84 bytes from 10.5.10.13 icmp_seq=5 ttl=64 time=0.554 ms
```

```bash
admin2> ping 10.5.10.11
84 bytes from 10.5.10.11 icmp_seq=1 ttl=64 time=0.398 ms
84 bytes from 10.5.10.11 icmp_seq=2 ttl=64 time=0.432 ms
84 bytes from 10.5.10.11 icmp_seq=3 ttl=64 time=0.416 ms
84 bytes from 10.5.10.11 icmp_seq=4 ttl=64 time=0.401 ms
84 bytes from 10.5.10.11 icmp_seq=5 ttl=64 time=0.387 ms

admin2> ping 10.5.10.13
84 bytes from 10.5.10.13 icmp_seq=1 ttl=64 time=0.290 ms
84 bytes from 10.5.10.13 icmp_seq=2 ttl=64 time=0.431 ms
84 bytes from 10.5.10.13 icmp_seq=3 ttl=64 time=0.387 ms
84 bytes from 10.5.10.13 icmp_seq=4 ttl=64 time=0.423 ms
84 bytes from 10.5.10.13 icmp_seq=5 ttl=64 time=0.396 ms
```

```bash
admin3> ping 10.5.10.11
84 bytes from 10.5.10.11 icmp_seq=1 ttl=64 time=0.363 ms
84 bytes from 10.5.10.11 icmp_seq=2 ttl=64 time=0.532 ms
84 bytes from 10.5.10.11 icmp_seq=3 ttl=64 time=0.557 ms
84 bytes from 10.5.10.11 icmp_seq=4 ttl=64 time=0.594 ms
84 bytes from 10.5.10.11 icmp_seq=5 ttl=64 time=0.500 ms

admin3> ping 10.5.10.12
84 bytes from 10.5.10.12 icmp_seq=1 ttl=64 time=0.435 ms
84 bytes from 10.5.10.12 icmp_seq=2 ttl=64 time=0.356 ms
84 bytes from 10.5.10.12 icmp_seq=3 ttl=64 time=0.400 ms
84 bytes from 10.5.10.12 icmp_seq=4 ttl=64 time=0.401 ms
84 bytes from 10.5.10.12 icmp_seq=5 ttl=64 time=0.379 ms
```

- ping guests entre eux

```bash
guest1> ping 10.5.20.12
84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.287 ms
84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.424 ms
84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.414 ms
84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.409 ms
84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.474 ms

guest1> ping 10.5.20.13
84 bytes from 10.5.20.13 icmp_seq=1 ttl=64 time=0.564 ms
84 bytes from 10.5.20.13 icmp_seq=2 ttl=64 time=0.534 ms
84 bytes from 10.5.20.13 icmp_seq=3 ttl=64 time=0.651 ms
84 bytes from 10.5.20.13 icmp_seq=4 ttl=64 time=0.543 ms
84 bytes from 10.5.20.13 icmp_seq=5 ttl=64 time=0.510 ms
```

```bash
guest2> ping 10.5.20.11
84 bytes from 10.5.20.11 icmp_seq=1 ttl=64 time=0.394 ms
84 bytes from 10.5.20.11 icmp_seq=2 ttl=64 time=0.394 ms
84 bytes from 10.5.20.11 icmp_seq=3 ttl=64 time=0.484 ms
84 bytes from 10.5.20.11 icmp_seq=4 ttl=64 time=0.553 ms
84 bytes from 10.5.20.11 icmp_seq=5 ttl=64 time=0.438 ms

guest2> ping 10.5.20.13
84 bytes from 10.5.20.13 icmp_seq=1 ttl=64 time=0.305 ms
84 bytes from 10.5.20.13 icmp_seq=2 ttl=64 time=0.465 ms
84 bytes from 10.5.20.13 icmp_seq=3 ttl=64 time=0.393 ms
84 bytes from 10.5.20.13 icmp_seq=4 ttl=64 time=0.413 ms
84 bytes from 10.5.20.13 icmp_seq=5 ttl=64 time=0.407 ms
```

```bash
guest3> ping 10.5.20.11
84 bytes from 10.5.20.11 icmp_seq=1 ttl=64 time=0.498 ms
84 bytes from 10.5.20.11 icmp_seq=2 ttl=64 time=0.565 ms
84 bytes from 10.5.20.11 icmp_seq=3 ttl=64 time=0.560 ms
84 bytes from 10.5.20.11 icmp_seq=4 ttl=64 time=0.530 ms
84 bytes from 10.5.20.11 icmp_seq=5 ttl=64 time=0.557 ms

guest3> ping 10.5.20.12
84 bytes from 10.5.20.12 icmp_seq=1 ttl=64 time=0.390 ms
84 bytes from 10.5.20.12 icmp_seq=2 ttl=64 time=0.430 ms
84 bytes from 10.5.20.12 icmp_seq=3 ttl=64 time=0.392 ms
84 bytes from 10.5.20.12 icmp_seq=4 ttl=64 time=0.399 ms
84 bytes from 10.5.20.12 icmp_seq=5 ttl=64 time=0.411 ms
```

### 3. VLAN

```bash
10   admins                           active
20   guests                           active
```

```bash
sw1#show interface trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       none
```

-  Vérifier et prouver qu'un guest qui prend un IP du réseau admins ne peut joindre aucune machine.

```bash
guest2> ip 10.5.10.22 /24 10.5.10.254
Checking for duplicate address...
PC1 : 10.5.10.22 255.255.255.0 gateway 10.5.10.254

guest2> save
Saving startup configuration to startup.vpc
.  done

guest2> ping 10.5.10.12
host (10.5.10.12) not reachable
```

```bash
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES NVRAM  up                    up  
FastEthernet1/0            unassigned      YES NVRAM  up                    up  
FastEthernet1/0.10         10.5.10.254     YES NVRAM  up                    up  
FastEthernet1/0.20         10.5.20.254     YES NVRAM  up                    up  
```

- switch du milieu aussi configuré pourtant

```bash
admin1> ping 10.5.10.254
host (10.5.10.254) not reachable
```

toutes les modifications ont étés faites mais mon router ne peut pas ping les vpcs et inversement
