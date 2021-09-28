
## Labo n°4 : VLSM et RIPv2

```diff
-> Morgan Valentin
```

#### Schéma du labo : 

<img src="img/labo_4.png">

---

#### Remarques

1. Ne pas mettre "`default-information originate`" si on a PAS de route à partager !
	* Donc juste pour R1, mais pas pour les autres.
2. Ne pas oublier de préciser les "clock rates" pour les liaisons entre routeurs
3. Mettre "`no auto-summary`" dans les configurations RIP.

---

#### Table d'adressage

| Host          | Network           | IP range                               | Broadcast     |
| ------------- | ----------------- | -------------------------------------- | ------------- |
| **LAN3 (60)** | 172.16.30.**0**   | 172.16.30.**1** => 172.16.30.**62**    | 172.16.30.63  |
| **LAN1 (20)** | 172.16.30.**64**  | 172.16.30.**65** => 172.16.30.**94**   | 172.16.30.95  |
| **LAN2 (7)**  | 172.16.30.**96**  | 172.16.30.**97** => 172.16.30.**110**  | 172.16.30.111 |
| **WAN12**     | 172.16.30.**112** | 172.16.30.**113** => 172.16.30.**114** | 172.16.30.115 |
| **WAN13**     | 172.16.30.**116** | 172.16.30.**117** => 172.16.30.**118** | 172.16.30.119 |
| **WAN23**     | 172.16.30.**120** | 172.16.30.**121** => 172.16.30.**122** | 172.16.30.123 |

---

#### Répartitions des adresses IP

| Host/Device      | Interface | IP              | Mask                  |
| ---------------- | --------- | --------------- | --------------------- |
| R1               | g0/0      | 172.16.30.65    | 255.255.255.224 (/27) |
| R1 (=> internet) | g0/1      | 209.162.210.200 | 255.255.255.0 (/24)   |
| R1 (=> R2)       | s0/0/0    | 172.16.30.113   | 255.255.255.252 (/30) |
| R1 (=>R3)        | s0/0/1    | 172.16.30.117   | 255.255.255.252 (/30) |

| Host/Device | Interface | IP            | Mask                  |
| ----------- | --------- | ------------- | --------------------- |
| R2          | g0/0      | 172.16.30.97  | 255.255.255.240 (/28) |
| R2 (=> R1)  | s0/0/0    | 172.16.30.114 | 255.255.255.252 (/30) |
| R2 (=> R3)  | s0/0/1    | 172.16.30.121 | 255.255.255.252 (/30) |

| Host/Device | Interface | IP            | Mask                  |
| ----------- | --------- | ------------- | --------------------- |
| R3          | g0/0      | 172.16.30.1   | 255.255.255.192 (/25) |
| R3 (=> R1)  | s0/0/0    | 172.16.30.118 | 255.255.255.252 (/30) |
| R3 (=> R2)  | s0/0/1    | 172.16.30.122 | 255.255.255.252 (/30) |

---

|R1 |
|---|

`hostname R1`\
`no ip domain-lookup`\
`enable secret class`\
`banner motd %Hello%`

> **int g0/0**\
> `description vers LAN1`\
> `ip add 172.16.30.65 255.255.255.224`\
> `no shut`

> **int g0/1**\
> `description vers INTERNET`\
> `ip add 209.162.210.200 255.255.255.0`\
> `no shut`

> **int s0/0/0**\
> `description vers R2`\
> `ip add 172.16.30.113 255.255.255.252`\
> `clock rate 64000`\
> `no shut`

> **int s0/0/1**\
> `description vers R3`\
> `ip add 172.16.30.117 255.255.255.252`\
> `clock rate 64000`\
> `no shut`

> **line vty 0 4**\
> `logging synchronous`\
> `password cisco`\
> `login`

> **line console 0**\
> `logging synchronous`\
> `password cisco`\
> `login`

`service password-encryption`

`ip route 0.0.0.0 0.0.0.0 g0/1`

> **router rip**\
> `version 2`\
> `passive-interface g0/0`\
> `network 172.16.30.64`\
> `network 172.16.30.112`\
> `network 172.16.30.116`\
> `default-information originate`\
> `no auto-summary`

---

|R2 |
|---|

`hostname R2`\
`no ip domain-lookup`\
`enable secret class`\
`banner motd %Hello%`\

> **int g0/0**\
> `description vers LAN2`\
> `ip add 172.16.30.97 255.255.255.240`\
> `no shut`

> **int s0/0/0**\
> `description vers R1`\
> `ip add 172.16.30.114 255.255.255.252`\
> `clock rate 64000`\
> `no shut`

> **int s0/0/1**\
> `description vers R3`\
> `ip add 172.16.30.121 255.255.255.252`\
> `clock rate 64000`\
> `no shut`

> **line vty 0 4**\
> `logging synchronous`\
> `password cisco`\
> `login`

> **line console 0**\
> `logging synchronous`\
> `password cisco`\
> `login`

`service password-encryption`

> **router rip**\
> `version 2`\
> `passive-interface g0/0`\
> `network 172.16.30.96`\
> `network 172.16.30.112`\
> `network 172.16.30.120`\
> `no auto-summary`

---

|R3 |
|---|

`hostname R3`\
`no ip domain-lookup`\
`enable secret class`\
`banner motd %Hello%`

> **int g0/0**\
> `description vers LAN3`\
> `ip add 172.16.30.1 255.255.255.192`\
> `no shut`

> **int s0/0/0**\
> `description vers R1`\
> `ip add 172.16.30.118 255.255.255.252`\
> `clock rate 64000`\
> `no shut`

> **int s0/0/1**\
> `description vers R2`\
> `ip add 172.16.30.122 255.255.255.252`\
> `clock rate 64000`\
> `no shut`

> **line vty 0 4**\
> `logging synchronous`\
> `password cisco`\
> `login`

> **line console 0**\
> `logging synchronous`\
> `password cisco`\
> `login`

`service password-encryption`

> **router rip**\
> `version 2`\
> `passive-interface g0/0`\
> `network 172.16.30.0`\
> `network 172.16.30.116`\
> `network 172.16.30.120`\
> `no auto-summary`

---

|S1 |
|---|

`hostname S2`\
`enable secret class`\
`banner motd %Hello%`

`ip domain-name henallux.be`\
`username userssh secret userssh`\
`crypto key generate rsa general-keys modulus 1024`\
`ip ssh version 2`

> **int vlan1**\
> `description vers PC1`\
> `ip add 172.16.30.66 255.255.255.224`\
> `no shut`

> **line vty 0 4**\
> `logging synchronous`\
> `password cisco`\
> `transport input ssh`\
> `login local`

> **line console 0**\
> `logging synchronous`\
> `password cisco`

`service password-encryption`

`ip default-gateway 172.16.30.65`

---

|S2 |
|---|

`hostname S2`\
`enable secret class`\
`banner motd %Hello%`

`ip domain-name henallux.be`\
`username userssh secret userssh`\
`crypto key generate rsa general-keys modulus 1024`\
`ip ssh version 2`

> **int vlan1**\
> `description vers PC2`\
> `ip add 172.16.30.98 255.255.255.240`\
> `no shut`

> **line vty 0 4**\
> `logging synchronous`\
> `password cisco`\
> `transport input ssh`\
> `login local`

> **line console 0**\
> `logging synchronous`\
> `password cisco`

`service password-encryption`

`ip default-gateway 172.16.30.97`

---

|S3 |
|---|

`hostname S3`\
`enable secret class`\
`banner motd %Hello%`

`ip domain-name henallux.be`\
`username userssh secret userssh`\
`crypto key generate rsa general-keys modulus 1024`\
`ip ssh version 2`

> **int vlan1**\
> `description vers PC3`\
> `ip add 172.16.30.2 255.255.255.192`\
> `no shut`

> **line vty 0 4**\
> `logging synchronous`\
> `password cisco`\
> `transport input ssh`\
> `login local`

> **line console 0**\
> `logging synchronous`\
> `password cisco`

`service password-encryption`

`ip default-gateway 172.16.30.1`

---

|PC1 |
|---|

 ipv4 address        | 172.16.30.67  
 ------------------- | -------------- 
 **Subnet Mask**     | 255.255.255.224  
 **Default Gateway** | 172.16.30.65   

|PC2 |
|---|

 ipv4 address        | 172.16.30.99  
 ------------------- | -------------- 
 **Subnet Mask**     | 255.255.255.240  
 **Default Gateway** | 172.16.30.97   

|PC3 |
|---|

 ipv4 address        | 172.16.30.3  
 ------------------- | -------------- 
 **Subnet Mask**     | 255.255.255.192  
 **Default Gateway** | 172.16.30.1   