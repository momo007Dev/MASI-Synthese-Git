
## Labo n°2 : Routage Statique

```diff
-> Morgan Valentin
```

#### Schéma du labo : 

<img src="img/labo_2.png">

---

#### Répartitions des adresses IP

| Host/Device | Interface | IP           | IPv6                              |
| ----------- | --------- | ------------ | --------------------------------- |
| R1          | g0/0      | 192.168.10.1 | `2001:DB8:ACAD:10::/64` => EUI-64 |
| R1 (=> R2)  | s0/0/0    | 10.10.10.1   | `2001:DB8:ACAD:100::1/64`         |
| R1 (=> R4)  | s0/0/1    | 10.10.10.9   | `2001:DB8:ACAD:102::1/64`         |

| Host/Device | Interface | IP           | IPv6                              |
| ----------- | --------- | ------------ | --------------------------------- |
| R2 (=> R1)  | s0/0/0    | 10.10.10.2   | `2001:DB8:ACAD:100::2/64`         |
| R2 (=> R3)  | s0/0/1    | 10.10.10.5   | `2001:DB8:ACAD:101::1/64`         |

| Host/Device | Interface | IP           | IPv6                              |
| ----------- | --------- | ------------ | --------------------------------- |
| R3          | g0/0      | 192.168.20.1 | `2001:DB8:ACAD:20::1/64`          |
| R3 (=> R4)  | s0/0/0    | 10.10.10.14  | `2001:DB8:ACAD:103::2/64`         |
| R3 (=> R2)  | s0/0/1    | 10.10.10.6   | `2001:DB8:ACAD:101::2/64`         |

| Host/Device | Interface | IP           | IPv6                              |
| ----------- | --------- | ------------ | --------------------------------- |
| R4 (=> R3)  | s0/0/0    | 10.10.10.13  | `2001:DB8:ACAD:103::1/64`         |
| R4 (=> R1)  | s0/0/1    | 10.10.10.10  | `2001:DB8:ACAD:102::2/64`         |


---

| RoNamur |
| ------- |

`hostname RoNamur`\
`no ip domain-lookup`

> **int g0/0/0**\
> `description vers namur`\
> `ip add 172.16.10.1 255.255.255.0`\
> `no shut`

> **int s0/1/0**\
> `description vers RoMarche`\
> `ip add 10.10.10.1 255.255.255.252`\
> `no shut`

! Route par défaut - Direct connecté \
`ip route 0.0.0.0 0.0.0.0 s0/1/0`

---

| RoArlon |
| ------- |

`hostname RoMarche`\
`no ip domain-lookup`

> **int g0/0/0**\
> `description vers marche`\
> `ip add 192.168.0.1 255.255.255.0`\
> `no shut`

> **int s0/1/0**\
> `description vers RoNamur`\
> `ip add 10.10.10.2 255.255.255.252`\
> `no shut`

> **int s0/1/1**\
> `description vers RoArlon`\
> `ip add 10.10.10.5 255.255.255.252`\
> `no shut`

! Vers Namur -Récursive \
`ip route 172.16.10.0 255.255.255.0 10.10.10.1`

! Vers Arlon -Récursive par défaut \
`ip route 0.0.0.0 0.0.0.0 10.10.10.6`

---

|PCA |
|---|

 ipv4 address        | 172.16.10.10   
 ------------------- | -------------- 
 **Subnet Mask**     | 255.255.255.0  
 **Default Gateway** | 172.16.10.1    

---

|PCB |
|---|

 ipv4 address        | 192.168.0.20   
 ------------------- | -------------- 
 **Subnet Mask**     | 255.255.255.0  
 **Default Gateway** | 192.168.0.1    

---

|PCC|
|---|

 ipv4 address        | 172.16.4.140    
 ------------------- | --------------- 
 **Subnet Mask**     | 255.255.255.128 
 **Default Gateway** | 172.16.4.129    