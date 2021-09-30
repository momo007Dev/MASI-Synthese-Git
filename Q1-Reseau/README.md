## Q1-Réseau

#### Liens :

* [Théorie (pdf)](pdf/Theorie.pdf)
* [Cheat-Sheet (pdf)](pdf/Cheat-sheet.pdf)

### TP

- Réseau Cours n°1 (_14 septembre_) : **Config de bases sur du matériel physique**
  - [Labo-1](Labo-1.md)
  - [Labo-1 (version pdf)](pdf/Labo-1.pdf)
- Réseau Cours n°2 (_21 septembre_) : **Routages statique**
  - [Labo-2](Labo-2.md)
  - [Labo-2 (version pdf)](pdf/Labo-2.pdf)
- Réseau Cours n°3 (_28 septembre_) : **Dual Stack et VLSM**
  - [Labo-3](Labo-3.md) (Dual Stack)
  - [Labo-3 (version pdf)](pdf/Labo-3.pdf)
  - [Notes-VLSM](pdf/Vlsm.pdf)

* Réseau Cours n°4 (*5 octobre*) : **Routage dynamique - RIPv2**
  * [Labo-4](Labo-4.md)
  * [Labo-4 (version pdf)](pdf/Labo-4.pdf)
* Réseau Cours n°5 (*12 octobre*) : **VLAN et routage inter-VLAN**
  * [Labo-5](Labo-5.md)
  * [Labo-5 (version pdf)](pdf/Labo-5.pdf)
* Réseau Cours n°6 (*19 octobre*) : **Examen blanc**
  * [Labo-6](Labo-6.md)
  * [Labo-6 (version pdf)](pdf/Labo-6.pdf)

---

### Notes - Théorie

#### "Vecteur de distance" vs "a état de lien" 

| Protocole   | Type de protocole       | Infos                                                        |
| ----------- | ----------------------- | ------------------------------------------------------------ |
| RIP / EIGRP | A "vecteur de distance" | On fait confiance à ses voisins.                             |
| OSPF        | A "état de lien"        | On récupère toutes les informations possible sur le réseau et on construit sa propre base de donnée. |

#### OSI

| Couches           | données transmises / utilisées | Infos                                                        |
| ----------------- | ------------------------------ | ------------------------------------------------------------ |
| Application       | Donnée                         | Fait le lien entre les processus réseaux et les applications |
| Présentation      | Donnée                         | Représentation des données                                   |
| Session           | Donnée                         | Communication entre les hôtes                                |
| Transport         | Segments                       | Assure le contrôle du transfert de bout en bout              |
| Réseau            | Paquet                         | Sa fonction principale est la sélection du chemin (routage)  |
| Liaison de donnée | Trame                          | Elle gère l'accès au média                                   |
| Physique          | Bits                           | Détermine comments les éléments binaires sont transportés sur un support physique |

#### Encapsulation

* Lorsque les données d'application descendent la pile de protocoles en vue de leur transmission sur le support réseau, différentes informations de protocole sont ajoutées à chaque niveau.


#### Couche 2

* Full duplex = Envois et réception simultanément possible
* Half duplex = Envois et réception simultanément PAS possible (l'un ou l'autre)

* CSMA/CD = Détection de collision (savoir quand on peut envoyer un msg quoi)

* LLC = Pilote de lacarte réseau. Logiciel qui interagit avec le matériel de la carte réseau.

* Sous-couche MAC a 2 fonctions :
	* Encapsuler les données
	* Contrôler l'accès au support
  
* FF:FF:FF:FF:FF:FF => Broadcast MAC

* 2 méthodes de transmission de trame sur un switch :
	* Store and Forward
  
    * Attends de recevoir la trame complète, vérifie son CRC(checksum quoi), si valide, va rechercher sur quelle interface envoyer la trame.
  
    * Voir ça comme TCP
  * Cut-through
       * Envois directement la trame à la cible, sans pour autant vérifier le CRC.
       * Voir ça comme UDP

#### ARP (Adress Resolution Protocol)

* Requete ARP
	* Je veux contacter 10.10.10.2 dans mon réseau. Je connais pas la MAC, donc j'envois un packet "who has (ip) tell (mon ip)" en broadcast
* Reponse ARP
	* Celui a qui l'ip est la sienne, va répondre "(ip) is at (mac)"

:warning: Particularité si pas dans le même réseau => On va demander la MAC de la passerelle, puis lui envoyer notre packet.

#### IPv6 :

* FF02::1 = Je parle IPv6
* FF02::2 = Je suis un routeur (donc permet de contacter les routeurs)
  
#### Routage :
* AD (Distance administrative) permet à un routeur qui fait du routage OSPF et RIP de choisir une ou l'autre distance.
* PK ? Car métrique pour RIP=nbr de saut et métique pour OSPF= Bande passante
#### MAC Adress flooding
* La table des address MAC d'un switch (CAM) est limité.
* Si un pirate inonde d'adresse MAC le switch jusqu'à ce que la table du switch soit saturée, le switch va passer en mode fail-open (envoit partout)

#### DHCP snooping
* Avoir 2ème serveur DHCP qui va répondre au discover.
* Système de port "trusted" et "untrusted" sur le switch

#### DHCP starvation attack (ou DHCP flooding)
* Remplir le pool dhcp avec pleins de discover afin qu'il ne puisse plus proposer d'ip.

---

### Cheat-Sheet

| Base similaire   |
| ---- |

#### Hostname / mot de passe

```
hostname R1
enable secret class
banner motd %hello%
no ip domain-lookup
service password-encryption
```

#### SSH

```bash
ip domain-name henallux.be
username ssh secret ssh
crypto key generate rsa general-keys modulus 1024
ip ssh version 2
ip ssh authentication-retries 2
ip ssh time-out 20

line vty 0 4 # (si routeur) (0 15 si switch)
   password cisco
   transport input ssh
   login local
```

| Routeur   |
| ---- |

#### RIP

```bash
router rip
   version 2
   passive-interface g0/0
   network 10.10.10.0
   no auto-summary
   default-information originate # (uniquement si partage une route statique)
```

#### Gestion des VLANS niveau routeur

> Par exemple, c'est l'interface g0/0/0 qui reli le switch qui donne les accès aux différents "end device" (PC par exemple).

```bash
int g0/0/0.10
   encapsulation dot1Q 10
   ip add 192.168.10.254 255.255.255.0
        
int g0/0/0.20
   encapsulation dot1Q 20
   ip add 192.168.20.254 255.255.255.0
    
int g0/0/0
   no shut # (très important de réveiller cette interface)
```

#### Vitesse d'horloge (clock rate) des interfaces séries (celle qui relie un routeur à un autre)

```
int s0/0/0
   description vers R2
   ip add 172.16.0.21 255.255.255.252
   clock rate 64000
   no shut
```

#### Route statique IPv4

```bash
ip route 0.0.0.0 0.0.0.0 g0/1
   # "0.0.0.0 0.0.0.0" = Par défaut
   # "g0/1" = directement connecté
    
ip route 192.168.10.0 255.255.255.0 172.16.0.22
   # "192.168.10.0 255.255.255.0" = statique
   # "172.16.0.22" = next hop (=récursive)
```

#### Route statique ipv6

```bash
ipv6 route 2001:DB8:ACAD:20::/64 s0/0/0
   # "2001:DB8:ACAD:20::/64" = statique
   # "s0/0/0" = directement connecté
```

#### IPv6

```bash
ipv6 unicast-routing # (pour activer ipv6)
    
int g0/0
   description vers Namur
   ip add 192.168.10.1 255.255.255.0
   ipv6 address 2001:DB8:ACAD:10::/64 eui-64 # Si on veut eui-64
   ipv6 address FE80::1 link-local # Adresse de lien local
   no shut
    
int s0/0/0
   description vers R2
   ip add 10.10.10.1 255.255.255.252
   ipv6 add 2001:DB8:ACAD:100::1/64
   ipv6 address FE80::1 link-local
   no shut
```

#### Serveur DHCP dans le routeur :

```bash
ip dhcp excluded-address 192.168.10.1

ip dhcp pool pool-ipv4
   network 192.168.10.0 255.255.255.0
   default-router 192.168.10.1
   dns-server 192.168.20.250
```
>:warning:Pour IPv6, si on utilise **SLAAC** (par défaut), les PC sont automatiquement configuré.

| Switch  |
| ---- |

#### Config IP pour le switch :
```bash
int vlan1
    description vers PC1
    ip add 10.10.10.251 255.255.255.0
    no shut

ip default-gateway 10.10.10.254
```

#### VLAN (Access / Trunk) :
```bash
int f0/1
    switchport mode accessc # Utilisé lors des liaisons avec des PCs.
    switchport access vlan 10

int g0/1
    switchport mode trunk # Utilisé lors des liaisons avec un routeur ou entre switch
```