- [Guide d'installation](#guide-dinstallation)
  - [Prérequis matériel / réseau](#prérequis-matériel--réseau)
- [1. Installation des 3 nodes Proxmox](#1-installation-des-3-nodes-proxmox)
- [2. Configuration réseau](#2-configuration-réseau)
  - [2.1 Isolation du trafic Corosync sur VLAN 99](#21-isolation-du-trafic-corosync-sur-vlan-99)
  - [2.2 Activation VLAN aware](#22-activation-vlan-aware)
  - [2.3 Configuration du switch](#23-configuration-du-switch)
- [3. Création du cluster](#3-création-du-cluster)
- [4. Configuration SSH](#4-configuration-ssh)
- [5. ZFS](#5-zfs)
  - [5.1 Création mirror ZFS (Node 3 - Infra)](#51-création-mirror-zfs-node-3---infra)
  - [5.2 Création datasets (Node 3 - Infra)](#52-création-datasets-node-3---infra)
    - [5.2.1 Dataset de stockage des disques vm : "vmdata"](#521-dataset-de-stockage-des-disques-vm--vmdata)
    - [5.2.2 Dataset de stockage des conteneurs : "lxc"](#522-dataset-de-stockage-des-conteneurs--lxc)
    - [5.2.3 Vérification](#523-vérification)
  - [5.3 Création mirror ZFS (Node 2 - Apps)](#53-création-mirror-zfs-node-2---apps)
    - [5.3.1 Datasets pour le stockage des disques vm : "vmdata"](#531-datasets-pour-le-stockage-des-disques-vm--vmdata)
    - [5.3.2 Datasets pour le stockage des conteneurs : "lxc"](#532-datasets-pour-le-stockage-des-conteneurs--lxc)
    - [5.3.3 Vérification](#533-vérification)
- [6. Réplication ZFS](#6-réplication-zfs)
  - [6.1 Configuration SSH](#61-configuration-ssh)
  - [6.2 Ajouter au stockage Datacenter](#62-ajouter-au-stockage-datacenter)
  - [6.3 Configurer la réplication](#63-configurer-la-réplication)
  - [6.4 Optimisation du stockage](#64-optimisation-du-stockage)
  - [6.5 Configuration HA Manager](#65-configuration-ha-manager)
    - [6.5.1 Ha manager](#651-ha-manager)
    - [6.5.2 Affinity Rules](#652-affinity-rules)
    - [6.5.3 Test](#653-test)
- [7. Firewall Proxmox](#7-firewall-proxmox)
- [8. Tailscale](#8-tailscale)
- [9. OPNsense](#9-opnsense)
  - [9.1 Création de la vm](#91-création-de-la-vm)
  - [9.2 Installation](#92-installation)
  - [9.3 Configuration](#93-configuration)
  - [9.4 Agent Qemu](#94-agent-qemu)
- [10. VLAN 20 MGMT](#10-vlan-20-mgmt)
  - [10.1 Firewall Proxmox](#101-firewall-proxmox)
  - [10.2 Configuration du switch](#102-configuration-du-switch)
  - [10.3 Configuration des Nodes](#103-configuration-des-nodes)
  - [10.4 Configuration OPNsense](#104-configuration-opnsense)
  - [10.5 Migration GUI Proxmox dans VLAN 20 MGMT](#105-migration-gui-proxmox-dans-vlan-20-mgmt)
  - [10.6 NAT OPNsense](#106-nat-opnsense)
  - [10.7 Migration UI switch](#107-migration-ui-switch)
- [11. Plugins OPNsense](#11-plugins-opnsense)
  - [11.1 Tailscale](#111-tailscale)
    - [11.1.1 Routage Tailscale](#1111-routage-tailscale)
  - [11.2 CrowdSec](#112-crowdsec)
  - [11.3 IDS (natif OPNsense basé sur suricata)](#113-ids-natif-opnsense-basé-sur-suricata)
- [12. Installations de CrowdSec sur les 3 nodes](#12-installations-de-crowdsec-sur-les-3-nodes)
  - [12.1 Création d'une sous-interface dans Proxmox](#121-création-dune-sous-interface-dans-proxmox)
  - [12.2 Création d'une interface dédiée sur OPNsense](#122-création-dune-interface-dédiée-sur-opnsense)
  - [12.3 Agents CrowdSec (Nodes)](#123-agents-crowdsec-nodes)
  - [12.4 Logs](#124-logs)
- [13. AdGuard Home (LXC ct-dns)](#13-adguard-home-lxc-ct-dns)
  - [13.1 Création du conteneur LXC](#131-création-du-conteneur-lxc)
  - [13.2 Installation AdGuard Home](#132-installation-adguard-home)
  - [13.3 Intégration dans OPNsense (pour forwarding via Unbound DNS)](#133-intégration-dans-opnsense-pour-forwarding-via-unbound-dns)
  - [13.4 Accès Tailscale](#134-accès-tailscale)
  - [13.5 Configuration DNS du conteneur](#135-configuration-dns-du-conteneur)
  - [13.6 Configuration Kea DHCP dans OPNsense](#136-configuration-kea-dhcp-dans-opnsense)
  - [13.7 Configuration AdGuardHome](#137-configuration-adguardhome)
- [14. LXC Zabbix](#14-lxc-zabbix)
  - [14.1 Installation](#141-installation)
  - [14.2 Intégration des nodes dans Zabbix](#142-intégration-des-nodes-dans-zabbix)
    - [14.2.1 Installer l'agent sur le node](#1421-installer-lagent-sur-le-node)
    - [14.2.2 Configurer l'agent](#1422-configurer-lagent)
    - [14.2.3 Configurer les firewalls](#1423-configurer-les-firewalls)
    - [14.2.4 Ajouter le node dans Zabbix](#1424-ajouter-le-node-dans-zabbix)
  - [14.3 Intégration LXC dans Zabbix](#143-intégration-lxc-dans-zabbix)
  - [14.4 Intégration OPNsense dans zabbix](#144-intégration-opnsense-dans-zabbix)
    - [14.4.1 Plugin SNMP](#1441-plugin-snmp)
    - [14.4.2 Intégration dans Zabbix](#1442-intégration-dans-zabbix)
    - [14.5 Configurer les notifications Telegram](#145-configurer-les-notifications-telegram)
- [15. Création de la DMZ](#15-création-de-la-dmz)
- [16. Vm Traefik en DMZ](#16--vm-traefik-en-dmz)
- [17. PBS (à venir)](#17-pbs-à-venir)
- [18. Sauvegardes 3-2-1 (à venir)](#18-sauvegardes-3-2-1-à-venir)
- [19. LXC apps (à venir)](#19-lxc-apps-à-venir)
- [20. VPS + Pangolin](#20-vps--pangolin)

# Guide d'installation

## Prérequis matériel / réseau  

- Accès Internet avec DMZ configurable
- 3 nodes Proxmox dans le sous-réseau de la box-Internet (temporairement)
- Switch manageable


 

# 1. Installation des 3 nodes Proxmox

- Préparer une clé d'installation Proxmox (ici Proxmox 9)

- Démarrer dans le BIOS et sélectionner la clé comme disque de démarrage.

- Suivre l'assistant d'installation :

    - IP du node
    - gateway : indiquer IP box Internet + dns 8.8.8.8 (pour le moment)
    - décocher : redémarrage automatique après installation

- Il est nécessaire de modifier le BIOS pour redémarrer sur le SSD externe

- Test avec *ping* et vérifier l'accès à l'interface web Proxmox à partir de l'IP affichée en fin d'installation ou après redémarrage.

- Test avec *lsblk -f* pour vérifier le disque d'installation et système de fichiers

- Modifier les dépôts Proxmox pour permettre les mises à jour système

    - Supprimer les dépôts entreprise 
  
  ![Repositories](../Screenshot/1_repositories-pve.png)

    - Ajouter les dépôts "pve-non-subscription"
    ![Repositories](../Screenshot/2_ajout_reposotories.png)

- Mettre à jour et appliquer
    ```bash
    apt update
    apt full-upgrade -y
    ```
- Redémarrer le serveur

- Répéter sur les 2 autres nodes



# 2. Configuration réseau 

## 2.1 Isolation du trafic Corosync sur VLAN 99

Nécessaire pour isoler le trafic et éviter les perturbations sur le réseau.

- Éditer le fichier */etc/hosts* sur chaque node
    - Ajouter les IP de chaque node 
    - Ajouter les IP des sous-réseaux Corosync + noms locaux des nodes


    ![/etc/hosts](../Screenshot/3_etc-hosts.png)


- Créer le VLAN 99 sur le switch pour trunk VLAN 
    - Ports 2/3/4 Tagged 


--> Le trafic Corosync -> tagged VLAN 99 -> seulement entre ports 2,3,4 (nodes Proxmox)


==> Faire un backup de la config du switch

- Créer un Linux VLAN 

![linux VLAN](../Screenshot/5_Linux_VLAN.png)

![alt text](../Screenshot/6_Linux_VLAN.png)

**--> "Apply Configuration"**


- Créer un Linux bridge


![Linux Bridge](../Screenshot/7_bridge.png)

IP= Adresse IP Corosync du node 

**--> "Apply Configuration"**

- Faire la même opération sur les 2 autres nodes.

## 2.2 Activation VLAN aware 

- Une seule interface physique disponible donc trunk 802.1Q nécessaire pour gérer les VLAN -> bridge VLAN aware + VLAN Tags sur vmbr0
   

- Sur chaque node -> network -> vmbr0 ->cocher "VLAN aware"


- Exemple de fichier de configuration __/etc/network/interfaces__ :

![Vlan_aware](../Screenshot/8_config-network.png)

- Pour recharger la configuration

    ```bash
    ifreload -a
    ```
## 2.3 Configuration du switch

| Ports | Élément |
|-------|-----------|
| 1     | Connexion box Internet |
| 2     | Node 1    |
| 3     | Node 2    |
| 4     | Node 3    |
| 8     | PC de configuration/tests |

- La configuration du switch est minimale et temporaire à ce stade ( Nodes derrière le NAT de la box Internet).
- Management Proxmox : VLAN natif --> ports 2/3/4/8 untagged
- VLAN 99 est configuré pour isoler le trafic Corosync --> ports 2/3/4 Tagged 
- VLAN 10  : a créer pour l'installation du pare-feu --> ports 2/3/4 tagged 
- La segmentation finale sera mise en place après installation du pare-feu
  
>--> [Détail du plan d'adressage réseau et configuration du switch](../Docs/configuration_reseau.md) 





# 3. Création du cluster

- Dans le Datacenter -> Cluster -> Create cluster -> indiquer le nom du cluster -> Create

- Cliquer sur "Join information" et copier les données


- Pour joindre les autres nodes -> Node -> Join Cluster -> coller les informations -> Join



![Cluster](../Screenshot/11_cluster.png)

- Entrer le mot de passe root
- Sélectionner le cluster network, utiliser IP Corosync 
- Rafraichir l'interface web (coches vertes si ok)


![Cluster](../Screenshot/12-cluster.png)

- Contrôler le quorum avec __pvecm status__

![Cluster](../Screenshot/13-0_pvecm.png)


# 4. Configuration SSH



- Installation de sudo 
    
    ```bash
    apt install sudo
    ```

- Création d'un utilisateur sur l'hôte proxmox et ajout dans le groupe sudo

    ```bash
    adduser adminuser

    usermod -aG sudo adminuser
    ```

- Création de la clé depuis un pc

    ```bash
    ssh-keygen -t ed25519 -C "proxmox3"
    ```

- Envoi de la clé sur le serveur :
    ```bash
        ssh-copy-id user@192.168.1.103
    ```

- Configuration fichier /etc/ssh/sshd_config
    ```bash
  Port 22  #choix pour conserver shell inter-nodes
  PermitRootLogin prohibit-password  #pour conserver accès UI proxmox
  PasswordAuthentication no
  PubkeyAuthentication yes
    ```
         

- Pour appliquer les modifications
    ```bash
    systemctl restart ssh
    ```


- Répéter sur tous les nodes




# 5. ZFS

## 5.1 Création mirror ZFS (Node 3 - Infra) 

- Nettoyage des NVMe internes avant utilisation (wipe disk)

![alt text](../Screenshot/14-wipe.png)

- Créer le pool ZFS (En CLI, création via GUI ne fonctionne pas avec disques de taille différente, il faut forcer) -> pour utilisation ID persistants Cf. [5.3](#53-création-mirror-zfs-node-2---apps)

    ```bash
    zpool create -f -o ashift=12 tank mirror /dev/nvme0n1 /dev/nvme1n1
    ```

- Vérification -> CLI :   **zfs list + zpool status**

```bash
root@proxmox3:~# zpool status
pool: tank
state: ONLINE
scan: scrub repaired 0B in 00:00:02 with 0 errors on Sun Jan 11 00:24:03 2026
config:

        NAME                                                                       STATE     READ WRITE CKSUM
        tank                                                                       ONLINE       0     0     0
        mirror-0                                                                 ONLINE       0     0     0
            nvme-nvme.1e4b-5938365730303834373454-4739333245312035313247-00000001  ONLINE       0     0     0
            nvme-nvme.1e4b-59364c5430303132383754-473933324551203154-00000001      ONLINE       0     0     0

errors: No known data errors
root@proxmox3:~# zpool list
NAME   SIZE  ALLOC   FREE  CKPOINT  EXPANDSZ   FRAG    CAP  DEDUP    HEALTH  ALTROOT
tank   476G  7.20G   469G        -         -     1%     1%  1.00x    ONLINE  -
root@proxmox3:~# 
```



## 5.2 Création datasets (Node 3 - Infra) 

- Structure :

    - Pool principal : tank

    - Datasets :

        - tank/vmdata → VM uniquement (compression=off, recordsize=128K).
        - tank/lxc → Conteneurs LXC rootfs (compression=lz4 (héritée), recordsize=default (héritée).
    


### 5.2.1 Dataset de stockage des disques vm : "vmdata"


```bash

# Propriétés globales héritées 
zfs set compression=lz4 tank
zfs set atime=off tank

# Création dataset
zfs create tank/vmdata

# Surcharges
zfs set compression=off tank/vmdata
zfs set recordsize=128K tank/vmdata


```


### 5.2.2 Dataset de stockage des conteneurs : "lxc"

```bash
# Création dataset
zfs create tank/lxc
```

### 5.2.3 Vérification

```bash
root@proxmox3:~# zfs get compression,recordsize,atime tank/vmdata
NAME         PROPERTY     VALUE           SOURCE
tank/vmdata  compression  off             local
tank/vmdata  recordsize   128K            local
tank/vmdata  atime        off             inherited from tank
root@proxmox3:~# zfs get compression,recordsize,atime tank/lxc   
NAME      PROPERTY     VALUE           SOURCE
tank/lxc  compression  lz4             inherited from tank
tank/lxc  recordsize   128K            default
tank/lxc  atime        off             inherited from tank

```

## 5.3 Création mirror ZFS (Node 2 - Apps)


- Nettoyage des NVMe internes avant utilisation  (CLI ou GUI)

    ```bash
    # Identifier les disques pour obtenir l'ID
    ls -l /dev/disk/by-id/

    # Nettoyer les disques
    wipefs -a /dev/disk/by-id/nvme-WD_BLACK_SN850X_2000GB_25197T801106
    wipefs -a /dev/disk/by-id/nvme-WD_BLACK_SN850X_2000GB_25197T802321
    ```


- Créer le pool ZFS (ici avec ID persistants)

    ```bash
    zpool create -f -o ashift=12 tank mirror /dev/disk/by-id/nvme-WD_BLACK_SN850X_2000GB_25197T802321 /dev/disk/by-id/nvme-WD_BLACK_SN850X_2000GB_25197T801106
    ```

- Vérification -> CLI :   **zfs list + zpool status**

 ```bash
 root@proxmox2:~# zfs list
NAME   USED  AVAIL  REFER  MOUNTPOINT
tank   408K  1.76T    96K  /tank
root@proxmox2:~# zpool status
  pool: tank
 state: ONLINE
config:

        NAME                                          STATE     READ WRITE CKSUM
        tank                                          ONLINE       0     0     0
          mirror-0                                    ONLINE       0     0     0
            nvme-WD_BLACK_SN850X_2000GB_25197T802321  ONLINE       0     0     0
            nvme-WD_BLACK_SN850X_2000GB_25197T801106  ONLINE       0     0     0

errors: No known data errors

 ```


### 5.3.1 Datasets pour le stockage des disques vm : "vmdata"

 (identiques par rapport à node 3)

 ```bash
 # Création dataset
 zfs create tank/vmdata

 #surcharge
 zfs set compression=off tank/vmdata
 zfs set recordsize=128K tank/vmdata
 ```


### 5.3.2 Datasets pour le stockage des conteneurs : "lxc"

```bash
# Création dataset
zfs create tank/lxc
```

### 5.3.3 Vérification 

```bash
root@proxmox2:~# zfs list
NAME          USED  AVAIL  REFER  MOUNTPOINT
tank          976K  1.76T   112K  /tank
tank/lxc       96K  1.76T    96K  /tank/lxc
tank/vmdata    96K  1.76T    96K  /tank/vmdata
root@proxmox2:~# zfs get compression,recordsize,atime tank/vmdata
NAME         PROPERTY     VALUE           SOURCE
tank/vmdata  compression  off             local
tank/vmdata  recordsize   128K            local
tank/vmdata  atime        off             inherited from tank
```


# 6. Réplication ZFS  

 - Réplication ZFS bidirectionnelle (par datasets, flux unidirectionnels). Cela concernera principalement les services infra (node 3) vers node 2 et quelques services Apps (node 2) vers node 3.




## 6.1 Configuration SSH

La réplication nécessite une connexion ssh root par clés entre les nodes concernés.

- Tester la connexion dans les 2 sens

Si besoin configurer SSH

- Générer la clé sur node 3 :

    ```bash 
    ssh-keygen -t ed25519 -C "replication-node3"
    ```
- Copier la clé publique vers le node2

    ```bash
    ssh-copy-id root@proxmox2
    ```

- Reproduire sur node2

- Tester la connexion dans les 2 sens

## 6.2 Ajouter au stockage Datacenter 

- Enregistrer une entrée ZFS au niveau du cluster en sélectionnant uniquement les nodes concernés (ici 2 et 3)


![zfs-cluster](../Screenshot/15_zfscluster.png)



## 6.3 Configurer la réplication

- Avec une vm  de test :

- Migrer le disque dans l'entrée zfs niveau cluster (vm -> Hardware -> Disk Action -> Move Storage )


![move-storage](../Screenshot/16_movestorage_zfs.png)


- Aller dans node -> Replication

    - Exemple :


    ![zfs-replication](../Screenshot/17_zfs-replication.png)

    => "*/15" = réplication toutes les 15 minutes

- Cliquer sur "Schedule Now" pour déclencher la première synchronisation.

![zfs-replication](../Screenshot/18_zfs_replication.png)

## 6.4 Optimisation du stockage 

Cette étape consiste à réorganiser les entrées ZFS de manière à stocker les disques vm ou LXC dans des datasets séparés.

- Créer les entrées ZFS dans l'interface proxmox (Datacenter -> Storage) en sélectionnant le dataset concerné (important ici -> uniquement nodes 2 et 3) :


![Optimisation zfs](../Screenshot/19_optimisation_zfs.png)

- Organisation finale :

![Optimisation zfs](../Screenshot/20_optimisation_zfs.png)

- Il est maintenant possible de migrer les disques des vm vers les entrées zfs correspondantes (vm-> Hardware -> Disk Action -> Move Storage ou sélectionner à l'installation). Les LXC doivent être arrêtés avant la migration du disque.

## 6.5 Configuration HA Manager

### 6.5.1 Ha manager

Nécessaire pour permettre le redémarrage d'une vm en cas de panne d'un node.

- Aller dans le Datacenter -> HA -> Add

![HA test](../Screenshot/21_ha_test.png)


- Vérification 

    ```bash
    root@proxmox3:~# ha-manager status
    quorum OK
    master proxmox2 (active, Fri Jan 23 11:49:30 2026)
    lrm proxmox1 (idle, Fri Jan 23 11:49:35 2026)
    lrm proxmox2 (idle, Fri Jan 23 11:49:35 2026)
    lrm proxmox3 (active, Fri Jan 23 11:49:35 2026)
    service vm:102 (proxmox3, started)
    ```
### 6.5.2 Affinity Rules

Nécessaire pour restreindre la relocalisation uniquement sur les nodes 2 et 3

- Aller dans Datacenter -> HA -> Affinity Rules -> HA Node Affinity Rules

![Affinity rules](../Screenshot/22_affinityrules.png)


### 6.5.3 Test

- Faire un backup de la vm 
- Débrancher le câble réseau du node 3 -> la vm migre automatiquement vers le node 2

>Note: Test réalisé, réplication fonctionnelle.


# 7. Firewall Proxmox 

Gestion du firewall Proxmox au niveau du Datacenter.Les nodes héritent des règles.

- Activer le firewall au niveau datacenter + nodes

- Enregistrer les règles dans le firewall Datacenter.


| Type | Action | Macro | Interface  | Protocol | Source | S.Port | Destination | D.Port | Comment |
|----|----|----|-----|-----|----|-----|----|------|------|
|in  | Accept |     |       | tcp  | 192.168.1.0/24 | | | 8006 | GUI Proxmox |
|in  | Accept |     |       | tcp  | 100.64.0.0/10 |  |  | 8006 | Tailscale Remote |
|in  | Accept | SSH |       |   | 100.64.0.0/10     |   |   |   | SSH Tailscale |
|in  | Accept | SSH |       |   | 192.168.1.0/24    |   |   |   | SSH local |
|in  | Drop  | SSH |        |    | 0.0.0.0/0        |   |   |   | Block SSH |
|in  | Drop  |     |        |tcp | 0.0.0.0/0        |   |    | 8006 | Block GUI Proxmox|




# 8. Tailscale

Installation  sur les 3 nodes.


- Créer un compte tailscale si besoin

- Ajouter un serveur linux ("Add device")

![device-tailscale](../Screenshot/24_add_device_tailscale.png)

- Cocher "Reusable" (clé réutilisable) -> cliquer sur "Generate install script"

![key-tailscale](../Screenshot/25_cle_reutilisable_tailscale.png)


- Exécuter le script indiqué en bas de page sur les 3 nodes

- Vérifier l'ajout des nodes dans l'interface web Tailscale


- Tester dans un navigateur internet __https://ip_tailscale_du_node:8006__ 
-> doit donner accès à la console Proxmox.


- Sur l'interface web de gestion Tailscale -> Machines -> clic droit sur le menu à coté des serveurs -> disable key expiry

![key-expiry-tailscale](../Screenshot/26_key_expiry_tailscale.png)

- Dans le shell du node vérifier l'état avec la commande __tailscale status__ 

> Note : L'accès aux nodes fonctionnera depuis un client Tailscale même si le firewall est inaccessible.



# 9. OPNsense


## 9.1 Création de la vm

- Importer l'iso sur le stockage local du node 3

![import_OPNsense](../Screenshot/27-0_import_opnsense.png)


- Créer la vm sur node 3
![OPNsense](../Screenshot/27-1_OPN.png)

>Note : il est possible d'ajouter la vm pour la HA ici mais il faudra configurer Affinity Rules après.


![OPNsense](../Screenshot/27-2_OPN.png)

![OPNsense](../Screenshot/27-3opensense.png)

![alt text](../Screenshot/27-4_opnsense.png)

![OPNsense](../Screenshot/27-5_OPN.png)

![OPNsense](../Screenshot/27-6_OPN.png) 

![OPNsense](../Screenshot/27-7_OPN.png) 

![alt text](../Screenshot/27-8_opnsense.png)



## 9.2 Installation

- Ajouter une deuxième interface à la vm pour l'interface LAN du firewall -> VLAN Tag10

![OPNsense](../Screenshot/27-9_LAN.png)


- Démarrer la vm 

- Installer OPNsense
![OPNsense](../Screenshot/27-10_OPN.png)

    login: installer
    Password: OPNsense

- Choisir le clavier


![OPNsense](../Screenshot/27-11_OPN.png) 
![OPNsense](../Screenshot/27-12_OPN.png) 
![OPNsense](../Screenshot/27-13_OPN.png) 
![OPNsense](../Screenshot/27-14_OPN.png) 
![OPNsense](../Screenshot/27-15_OPN.png)
![OPNsense](../Screenshot/27-16_OPN.png) 
![OPNsense](../Screenshot/27-17_OPN.png) 
![OPNsense](../Screenshot/27-18_OPN.png) 
![OPNsense](../Screenshot/27-19_OPN.png) 
![OPNsense](../Screenshot/27-20_OPN.png)


- Redémarrer la vm

- Configurer IP sur les interfaces WAN + LAN (Choix 2)

> Note : L'interface LAN OPNsense correspondra à VLAN 10.

- Créer un vm client de test (ici ubuntu) --> lors de la création :  tag 10 sur l'interface réseau vmbr0 pour obtenir IP dans la LAN d'OPNsense via DHCP natif)

- Accéder à l'interface Web via IP LAN 
- Sur la console OPNsense -> System -> Firmware -> Updates -> lancer la mise à jour (vérifier si plusieurs disponibles)



## 9.3 Configuration

- Enregistrer les règles pour interface LAN

![OPNsense](../Screenshot/27-22_rules_OPN.png)

- Interface WAN -> règles par défaut

- Activer la DMZ de la box internet pour router le trafic vers l'IP WAN d'OPNsense (Redémarrer la box)

- Vérifier les fonctionnalités ping et accès GUI depuis un client de test.



## 9.4 Agent Qemu

- Aller dans System-> Plugins : cocher "showcommunity plugins" et rechercher "os-qemu-guest-agent"

![OPNsense-qemu](../Screenshot/27-24_qemu_OPN.png)

- Cliquer sur le "+" pour installer

- Aller dans le shell OPNsense pour activer :

```bash
    sysrc qemu_guest_agent_enable="YES"

    service qemu-guest-agent start
```

- Vérifier dans la console Proxmox de la vm : l'IP de la vm doit maintenant être visible.

# 10. VLAN 20 MGMT

L'objet est d'isoler le trafic management Proxmox  dans un VLAN dédié.

--> [Détail du plan d'adressage réseau et configuration du switch](../Docs/configuration_reseau.md) 



## 10.1 Firewall Proxmox 

- Enregistrer ces règles au niveau  Datacenter

| type | action | macro | protocol | source | port de destination | Rôle |
|-------|-------|--------|---------|----------|--------------------|------|
| in    | Accept | Ping |           | 10.10.20.0/24| | Autorise ICMP sur VLAN 20 MGMT|
| in    | Accept |      | tcp       | 10.10.20.0/24 | 8006  | Autorise UI Proxmox sur VLAN 20 MGMT|




## 10.2 Configuration du switch

- Faire un backup de la configuration du switch
- Ajouter une IP statique (VLAN 20) sur le PC de configuration 
- Créer VLAN 20 MGMT dans l'UI du Switch :  
  - Ports 2/3/4/8
  - Tagged 2/3/4
  - Untagged 8 
  - PVID port 2/3/4 = 1 / Trames Tagged-only (Temporaire)
  - PVID port 8 = 20



## 10.3 Configuration des Nodes

  
- Dans le shell du node : créer la sous interface dans __/etc/network/interfaces__ (gateway non renseignée pour le moment)

    ```bash

    auto vmbr0.20
    iface vmbr0.20 inet static
        address 10.10.20.101/24
    #vlan 20 MGMT

    systemctl restart networking
    ```

- Vérifier avec la commande __ip a__ 
- Reproduire sur les autres nodes
  
## 10.4 Configuration OPNsense
  
- Ajouter une interface à OPNsense dans Proxmox : Node -> vm OPNsense -> Hardware

![vlan20](../Screenshot/36-1_vlan20.png)

- Ajouter l' interface dans OPNsense : OPNsense -> Interfaces -> Assignements -> Add -> Enable -> Attribuer IP statique -> save -> Apply changes

- Redémarrer la vm et vérifier IP du nouvel interface dans le shell OPNsense

- Appliquer les règles sur l'interface MGMT


| Direction| action |Protocol|	Source|	Port	|Destination|	Port|	Gateway	|Schedule|		Description |
|-------|-------|--------|---------|----------|--------|------------|------|----|------|
| in | Pass | tcp| MGMTnet| * | This Firewall | 443 |  *| *|Autorise accès UI OPNsense sur MGMT VLAN 20  |
| in | Pass |IPv4* |	opt3_MGMT net|	*	|*|	*|	*	|*	|	Accès Internet MGMT|
| in | Pass |	IPv4 ICMP|	opt3_MGMT net|	*|	This Firewall|	* |	*|	*	|	ICMP  VLAN 20 vers OPNsense|


- Tester la connexion à l'UI Proxmox depuis machine dans VLAN 20 (pc ou vm)
  
## 10.5 Migration GUI Proxmox dans VLAN 20 MGMT

- Pour transférer le management dans VLAN 20 : se connecter à l'UI  Proxmox via IP MGMT (VLAN20)
  
- Node -> Network -> vmbr0 : supprimer IP+Gateway
- Node -> Network -> vmbr0.20 : ajouter Gateway 

--> L'accès GUI via IP dans le sous réseau de la box internet est désactivé.

- Tester la connexion à l'UI proxmox depuis machine dans VLAN 20 (pc ou vm)
- Modifier __/etc/hosts__ sur chaque noeuds et modifier avec le nouvelles IP dans VLAN 20

- Rétablir shell inter-nodes en ajoutant une règle dans la firewall du Datacenter
  
| direction | action | source | macro | Rôle |
|-----------|-----------|----|-------|------|
|in | Accept | 10.10.20.0/24 | SSH | SSH inter-nodes - 

- Vérifier sur chaque node que tout les shell node sont accessibles


## 10.6 NAT OPNsense

Dans le contexte de ce lab un accès internet sera autorisé pour VLAN 20 (Updates, dépôts...etc).

> Note : OPNsense ne reconnait pas l'interface MGMT comme un LAN et ne crée pas la règle NAT automatique. Il est donc nécessaire de créer une règle spécifiques pour VLAN 20. 

- OPNsense -> Firewall -> Nat -> Outbound -> cocher "Hybrid outbound NAT rule generation"
  
- Créer une règle NAT 

|Interface	| Source	| Source Port	| Destination	| Destination Port |	NAT Address	| NAT Port	| Static Port |	Description	|
|------|----|-----|----|----|----|----|----|----|
|WAN	|opt3_MGMT net	| *|	*|	*|	Interface address	|*|	NO|	NAT MGMT VLAN 20|


- Tester la connexion internet depuis les nodes avec la commande __apt update__

## 10.7 Migration UI switch

Le but est de placer l'UI du switch dans vlan 20 pour l'isoler du sous-réseau de la box Internet.

- Brancher le PC sur le port 8 et configurer une IP statique (test ping vers gateway VLAN 20)
- Faire un backup de la configuration du switch
- Modifier l'IP du switch et la passerelle (VLAN 20)
- Récupérer l'UI sur la nouvelle IP depuis le PC (+ test Tailscale, doit fonctionner aussi)



# 11. Plugins OPNsense 

## 11.1 Tailscale

- OPNsense -> system-> firmware -> plugins -> cocher "show community plugins" -> rechercher le plugin -> cliquer sur "+" pour installer


- Sur l'interface web Tailscale -> Settings -> Personal settings -> Keys : générer une nouvelle clé (cocher reusable)

- OPNsense -> VPN -> Tailscale -> Authentication : coller la clé -> Apply

- OPNsense -> VPN -> Tailscale -> settings -> cocher enabled -> Apply

- OPNsense -> VPN -> Tailscale -> status -> copier la ligne "AuthURL" et la coller dans le navigateur -> s'authentifier sur le compte tailscale

- OPNsense -> Interfaces -> Assignments -> ajouter et nommer l'interface  crée par le plug in (Tailscale Remote)

- Aller dans Interfaces -> Tailscale Remote -> Enabled -> save

![tailscale](../Screenshot/28-5_int_tailscale-remote.png)


- Ajouter une règle sur l'interface Tailscale Remote pour autoriser le trafic HTTPS et accès distant à l'UI OPNsense

    Protocol|	Source|	Port|	Destination|	Port|	Gateway	|Schedule	|	Description |
    |---------|--------|-----|--------------|-------|-----------|-----------|-------------|
    |IPv4 TCP|	*|	*|	This Firewall	|443 (HTTPS)|	*|	*	|	Allow Tailscale to Firewall	|

> Note : Cette règle n'autorise que des clients Tailscale.


- Tester la connexion dans le navigateur : __https://IP_tailscale_OPNsense__ -> accès à la console OPNsense

### 11.1.1 Routage Tailscale 

> Note : Le plugin permet un accès distant à  l'infrastructure en fonction des routes autorisées.

- Créer les règles sur l'interface Tailscale_Remote pour autoriser le trafic vers VLAN 20

    |Protocol|	Source|	Port|	Destination|	Port|	Gateway	|Schedule	|	Description |
    |---------|--------|-----|--------------|-------|-----------|-----------|-------------|
    |IPv4 TCP|	opt1_Tailscale_Remote net|	*	|opt3_MGMT net|	22 (SSH)|	*	|*	|	Allow SSH Tailscale to VLAN 20 MGMT	|   
    |IPv4 TCP|	opt1_Tailscale_Remote net|	*|	opt3_MGMT net	|8006|	*	|*	|	Allow Tailscale to vlan 20 MGMT	   |
  
   
 







- Configurer le routage :  OPNsense -> VPN -> Tailscale -> Settings -> onglet Advertised Routes
    

- Entrer l'adresse du réseau à autoriser  -> Save -> Apply
  
- Dans la console Tailscale -> approuver le subnet
- Depuis client tailscale tester dans la connection dans le navigateur https://10.10.20.1 -> accès à l'UI OPNsense via Tailscale


## 11.2 CrowdSec


- Aller dans System -> Firmware -> Plugins -> rechercher le plugin -> installer avec "+"

![crowdSec](../Screenshot/29_Crowdsec.png)

 
- Créer un compte CrowdSec si besoin (https://app.Crowdsec.net/)

- Cliquer sur "Enroll command" et copier la clé

- Dans OPNsense : menu Services -> CrowdSec -> Onglet settings -> Coller la clé  dans "Enrollment key..." -> Apply. (Active le service et ajoute les contributions communautaires)

- Retourner sur la page web CrowdSec -> Engines -> Accept enroll

- La mise à jour du plugin dans CrowdSec peut être longue (20/30 minutes)
  





## 11.3 IDS (natif OPNsense basé sur suricata)

- Aller dans Services -> Intrusion Detection -> Administration -> Onglet Settings -> cocher Enabled pour activer (ne pas cocher IPS) -> Cocher Enable syslog pour intégration des log à OPNsense ->  Rotate log : Daily -> Savelogs 7 (7 jours) -> Apply

![IDS](../Screenshot/30-0_IDS.png)



- Dans l'onglet Download -> sélectionner les règles de menaces courantes :

    - abuse.ch/URLhaus               -> URL malveillantes
    - abuse.ch/SSL IP Blacklist     -> IP avec certificats malveillants
    - abuse.ch/Feodo Tracker        -> Botnets actifs
    - ET open/emerging-attack_response -> machines infectées qui répondent à un attaquant
    - ET open/emerging-coinminer    -> malwares de minage crypto
    - ET open/emerging-exploit      -> tentatives d’exploitation de failles
    - ET open/emerging-exploit_kit  -> kits d’attaque web connus
    - ET open/emerging-malware      -> malwares connus
    - ET open/emerging-phishing     -> phishing
    - ET open/emerging-botcc        -> communications vers serveurs de botnets
    - ET open/emerging-botcc.portgrouped -> idem, ciblé sur ports suspects
    - ET open/emerging-dns -> requêtes dns typiques de malwares
    - ET open/threatview_CS_c2-> serveurs de contrôle malwares
    - ET open/compromised-> contact avec machine déjà infectées
    - ET open/emerging-scan-> détection scan réseaux
---

- Cliquer sur Enable Selected
   
- Retourner dans onglet Administration -> settings -> apply
- Onglet Downloads -> Enable Selected Rules (applique les règles)

- Onglet Administration -> Schedule (pour mise a jour automatique )

![alt text](../Screenshot/30-1_IDS.png)


__--> Save + Apply__

Pour consulter les alertes : Services -> Intrusion Detection -> Administration -> Alerts




> Note : Faire un backup complet de la vm pour sauvegarder la configuration complète.(clic droit sur la vm concernée). Ajouter à la réplication également [cf. Réplication ZFS](#6-réplication-zfs)
 



# 12. Installations de CrowdSec sur les 3 nodes

Le but est d'utiliser OPNsense comme élément central pour CrowdSec. 
Cela nécessite la création d'une interface réseau dédiée sur Proxmox et sur le firewall pour permettre le trafic entre les agents et le plugin CrowdSec.

## 12.1 Création d'une sous-interface dans Proxmox

- node -> network -> create -> Linux VLAN :

![VLAN30](../Screenshot/31_vlan30.png)

- Reproduire sur les autres nodes avec IP dans VLAN 30
  
## 12.2 Création d'une interface dédiée sur OPNsense

Pour ajouter l'interface réseau à la vm OPNsense (sur vmbr0  VLAN Tag 30)

- OPNsense -> Interfaces -> Assignments -> Add

- OPNsense -> Interfaces -> sélectionner la nouvelle interface -> cocher enable interfaces -> IPV4 configuration type = Static -> Entrer IP dans la plage choisie pour VLAN 30 (ici 10.10.30.1) -> save -> __Apply Configuration__

- Créer une règle pour autoriser les nodes (IP en VLAN 20 MGMT) vers VLAN 30
  
|Protocol	|Source	|Port|	Destination|	Port|	Gateway	Schedule|		Description |
|-----------|-------|-----|------------|----------|-----------------|------------------|
|IPv4 TCP|	opt3_MGMT net|	*|	10.10.30.1	|8080	|*	|*	|	Allow VLAN 20 to VLAN 30 Crowdsec|




- OPNsense -> Services -> CrowdSec -> onglet Settings -> modifier "LAPI listen port" 10.10.30.1 port 8080.

## 12.3 Agents CrowdSec (Nodes)


- Installation de l'agent CrowdSec

    ```bash
    curl -s https://install.crowdsec.net | sh
    apt update
    apt install  crowdsec -y

    #vérification
    systemctl status crowdsec
    ```

- Sur le node (pour enregistrement de l'agent dans OPNsense -> IP interface VLAN 30)
    ```bash
    cscli lapi register -u http://10.10.30.1:8080
    ```
- Dans le shell OPNsense
    ```bash
    cscli machine list

    cscli machine validate <numéro_de_machine>
    ```
- Sur le node 
  ```bash
  systemctl restart crowdsec

  #Vérification
  cscli lapi status
  ```
- Vérifier que tous les nodes sont ajoutés (la console CrowdSec se met à jour en décalé.)
    ```bash
    cscli machine list
    ```
- Vérifier les metrics sur chaque node : __cscli metrics__
    
![Metrics](../Screenshot/33_metrics_crowdsec.png)

> Les metrics montrent que tout n'est pas parsé, une configuration des logs est nécessaire.

## 12.4 Logs


  
- Installer sur le node la collection communautaire disponible [ici](https://app.crowdsec.net/hub/author/fulljackz/collections/proxmox) 
```bash
cscli collections install fulljackz/proxmox
```
- Editer le fichier __/etc/crowdsec/acquis.d/proxmox-logs.yaml__ pour créer le fichier d'acquisition de logs

```yaml
filenames:
  - /var/log/auth.log
labels:
  type: syslog
```

- Redémarrer crowdsec

- Pour tester, faire plusieurs tentatives de connexion ssh invalides directement sur le node
```bash
ssh xxx@127.0.0.1
```

- Vérifier avec la commande __cscli metrics__

![metrics](../Screenshot/33-1_metrics_crowdsec.png)

--> Le parsing fonctionne pour tous les nouveaux évènements et la détection pour SSH fonctionne également.

- Reproduire sur les autres nodes
 
# 13. AdGuard Home (LXC ct-dns) 

## 13.1 Création du conteneur LXC

- Créer le conteneur sur node 3

    - Unprivileged / Nested décoché
    - Template Debian 12 (Stable)
    - Disque : tank-lxc-node3 / 12 Go
    - CPU 1
    - 512 Mo de RAM
    - Interface : vmbr0 / VLAN Tag 10
    - Firewall coché
    - IP 10.10.10.50/24
    - Gateway 10.10.10.1
    - DNS 1.1.1.1

- Faire la mise à jour système

    ```bash
    apt update && apt upgrade -y
    ```

## 13.2 Installation AdGuard Home

__Architecture: Clients LAN -> OPNsense/Unbound (DNS) ->  AdGuardHome (filtrage) -> Quad9 (DoH)__

- Télécharger la version depuis le site [AdGuard](https://github.com/AdguardTeam/AdGuardHome/releases/latest)

    ```bash
    wget https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.107.71/AdGuardHome_linux_amd64.tar.gz
    ```
- Décompresser le fichier et installer
    ```bash
    tar -xzvf AdGuardHome_linux_amd64.tar.gz
    cd AdguardHome/
    ./AdGuardHome -s install

    #vérification
    systemctl status AdGuardHome.service
    ```

## 13.3 Intégration dans OPNsense (pour forwarding via Unbound DNS)

- Console OPNsense -> Services -> Unbound DNS -> General -> interface = LAN uniquement -> Apply


![Unbound](../Screenshot/34-0_Unbound.png)

- Console OPNsense -> Services -> Unbound DNS -> Query Forwarding
  

![Query-forwardind](../Screenshot/34-1_Query-forwarding.png)


__--> Apply__

- Console OPNsense -> Services -> Unbound DNS -> General -> Restart (bouton dans l'interface)

- La suite de la configuration se fait en ssh depuis une vm du LAN OPNsense.
  
    - Autoriser temporairement un accès administrateur (root) pour pouvoir effectuer une redirection de port via un tunnel ssh depuis la vm (le terminal doit rester ouvert):
        ```bash
        ssh -L 3000:127.0.0.1:3000 root@10.10.10.50
        ```
    - Accéder à l'UI AdGuard dans le navigateur de la vm:
        ```bash
        http://localhost:3000/install.html
        ```
- Configurer le wizard
    - Modifier le port de l'ui : 3000 -> terminer le wizard
    - Fermer le terminal et tester la connexion à l'UI web depuis la vm
        ```bash
        http://10.10.10.50:3000
        ```
       

- Vérifier les ports écoutés par le conteneur :
    ```bash
    ss -tulnp | grep -E ':(53|3000)'
    ```
- Tester la résolution dns via la vm du LAN (au niveau du firewall)
    ```bash
    nslookup google.com 10.10.10.1
    ```
- Après validation, modifier la configuration ssh du conteneur pour supprimer l'accès administrateur.

## 13.4 Accès Tailscale 

- Créer les règles sur l'interface Tailscale_Remote pour autoriser le trafic vers le conteneur



| Protocol	| Source |	Port |	Destination	| Port	|Gateway |	Schedule | Description |
|----|-----|----|----|-----|-----|------|-----|
|IPv4 TCP|	*	|*|	This Firewall	|443 (HTTPS)|	*|	*	|	Allow Tailscale to Firewall |	 	   
| IPv4 *|	opt1_Tailscale_Remote net |	*|	10.10.10.50/32|	* |	*|	*|		Tailscale Adguard GUI|

- Configurer le routage pour permettre accès Tailscale à VLAN 10
    - Console OPNsense -> VPN -> Tailscale -> Settings -> onglet Advertised Routes
    

    ![subnet -routing](../Screenshot/34-3_routes.png)

    __--> Apply__
  
- Dans la console Tailscale -> approuver le subnet
- Depuis PC connecté au réseau tailscale tester dans le navigateur
    ```bash
    http://IP_du_conteneur:3000
    ```
    L'accès à la GUI AdGuard via Tailscale fonctionne.

## 13.5 Configuration DNS du conteneur 

- Rectifier le DNS du LXC ct-dns. La modification est à effectuer au niveau du node pour une configuration persistante. 
  
  ```bash
  nano /etc/pve/<ID_du_lxc>.conf

  # Ajouter
  nameserver <IP LAN du firewall>

  pct reboot <ID_ct>
  ```

- Vérifier la configuration dans le conteneur
  ```bash
  cat /etc/resolv.conf
  ```

## 13.6 Configuration Kea DHCP dans OPNsense

Kea permet de séparer les services DNS et DHCP

- Console OPNsense -> Services -> Dnsmasq DNS&DHCP -> Décocher enable pour désactiver-> Apply
- OPNsense -> Service -> ISC DHCP V4 (legacy) -> Vérifier qu'il est désactivé.
- OPNsense -> Service -> Kea DHCP -> Control Agent -> Enable -> Apply
- OPNsense -> Service -> Kea DHCP -> Kea DHCPv4 -> Onglet settings :

  
    ![kea_dhcp](../Screenshot/35-0_kea_dhcp.png)
    

    __--> Apply__


- OPNsense -> Service -> Kea DHCP -> Kea DHCPv4 -> Onglet Subnets

    
    ![kea_dhcp](../Screenshot/35-1_kea_dhcp.png)

- Redémarrer le service via GUI OPNsense

- Depuis un client du LAN faire les vérifications
    ```bash
    ip a
    ip route
    resolvectl status
    nslookup google.com
    ```

    >Note : le DNS sortant est chiffré via DoH depuis AdGuard Home vers Quad9. Les clients utilisent uniquement OPNsense comme point de résolution.

- Faire une réservation dhcp pour le conteneur ct-dns : OPNsense -> Services -> Kea DHCPv4 : compléter IP , MAC Address et hostname.
- Modifier le conteneur pour attribution IP via DHCP et le redémarrer -> vérifie attribution DHCP.


- Faire un backup du conteneur et de la vm OPNsense

## 13.7 Configuration AdGuardHome

- UI AdGuardHome -> Paramètres généraux -> vérifier que le journal est activé
- UI AdGuardHome -> Filtres -> vérifier liste que la liste AdGuard DNS filter est cochée
- UI AdGuardHome -> Filtres -> Services bloqués : pour choisir les services à bloquer.
- UI AdGuardHome -> Paramètres DNS -> cocher "activer DNSSEC" (pour authenticité des réponses DNS)


# 14. LXC Zabbix 

## 14.1 Installation

- Créer le datatset 

    ```bash
    zfs create tank/lxc/monitoring
    ```
- Créer le conteneur LXC :
    - Template Debian 12
    - 2 CPU
    - 4Go RAM
    - Disque sur tank-lxc-node3 : 20 Go
    - Réseau : Tag VLAN 10


- Faire la mise a jour du conteneur

- Installer zabbix (Server, Frontend, Agent)
```bash
wget https://repo.zabbix.com/zabbix/7.4/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.4+debian12_all.deb

dpkg -i zabbix-release_latest_7.4+debian12_all.deb
apt update
apt install mariadb-server
systemctl enable mariadb
apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent


# Créer la base de données
mysql -uroot -p

create database zabbix character set utf8mb4 collate utf8mb4_bin;
#Création du mot de passe de la base de donnée pour l'utilisateur "zabbix"
create user zabbix@localhost identified by 'choix_du_MOT_DE_PASSE';
grant all privileges on zabbix.* to zabbix@localhost;
set global log_bin_trust_function_creators = 1;
quit;


#Importer le schéma
zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
#Entrer le mot de passe de l'utilisateur "zabbix
"
#Désactiver l’option log_bin_trust_function_creators
mysql -uroot
set global log_bin_trust_function_creators = 0;
quit;

#Configurer la base de données
Edit file /etc/zabbix/zabbix_server.conf
DBPassword=MOT_DE_PASSE
# mot de passe = idem DB

# Redémarrer et activer server+agent
systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2
```

- Dans un navigateur, se connecter à l'UI http://IP_du_ct/zabbix

- Lancer la configuration

- Se connecter avec les identifiants par défaut : Admin/zabbix

- changer mot de passe admin : User settings-> profile -> Change password


## 14.2 Intégration des nodes dans Zabbix

### 14.2.1 Installer l'agent sur le node

- Ajouter le dépot Zabbix (Pour Proxmox VE9 -> paquets Debian 13)
```bash
wget https://repo.zabbix.com/zabbix/7.4/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.4+debian13_all.deb

dpkg -i zabbix-release_latest_7.4+debian13_all.deb
apt update
```
- Installer l'agent
```bash
apt install zabbix-agent -y
```

### 14.2.2 Configurer l'agent
- Éditer __/etc/zabbix/zabbix_agentd.conf__ pour configurer l'agent
```bash
#indiquer IP du server zabbix
Server=10.10.10.40
ServerActive=10.10.10.40
Hostname=proxmox1
```
- Activer et redémarrer l'agent
```bash
systemctl enable zabbix-agent
systemctl restart zabbix-agent
```
- Sur le LXC ou est installé le serveur zabbix, tester la connexion avec le node
```bash
apt install zabbix-get -y
zabbix_get -s 10.10.20.101 -k system.uptime
```
>Note : Echec pour le premier node -->   configuration des firewalls nécessaire

### 14.2.3 Configurer les firewalls 

- Autoriser l'agent zabbix  dans le firewall Proxmox (Datacenter)

| Type | Action | Protocol | Source | D.Port | Comment|
|------|--------|----------|-------|----------|--------|
|in     | Accept| tcp       | 10.10.10.40| 10050 | Allow zabbix agent |
 
- Autoriser la communication entre node et serveur zabbix dans OPNsense en créant une règle sur interface VLAN10

|Protocol|	Source	|Port|	Destination	|Port	|Gateway|	Schedule	|	Description|
|--------|-----------|---|---------------|-------|------|-------------|---------------|
|IPv4 TCP|	10.10.10.40/32|	*	|VLAN20_MGMT net|	10050|	*|	*	|	Allow Zabbix server to VLAN20|

- Tester la connexion avec le node
```bash
zabbix_get -s 10.10.20.101 -k system.uptime
```


### 14.2.4 Ajouter le node dans Zabbix

- Pour le premier node créer un groupe "Infra-Nodes" : Data collection -> host group -> create host group (coin supérieur droit de l'écran)

- Créer le host pour le node : -> Data collection -> Hosts -> Create host

![alt text](../Screenshot/37_zabbix.png)

- Quand l'intégration est terminée les indicateurs passent au vert

![alt text](../Screenshot/37-1_zabbix.png)

- Reproduire sur les autres nodes


## 14.3 Intégration LXC dans Zabbix

- Suivre le [mode opératoire utilisé pour les nodes](#1421-installer-lagent-sur-le-node) mais adapter le dépôt zabbix pour debian 12
```bash
wget https://repo.zabbix.com/zabbix/7.4/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.4+debian12_all.deb
dpkg -i zabbix-release_latest_7.4+debian12_all.deb
apt update
```


## 14.4 Intégration OPNsense dans zabbix

### 14.4.1 Plugin SNMP

- Installer le plugin : OPNsense -> Firmware -> Plugins -> cocher "show community plugins" et chercher SNMP -> Installer le plugin "os-net-snmp"
- OPNsense -> Services -> Net-SNMP : paramétrer l'écoute pour que snmp ne réponde que sur VLAN 20 -> save

![zabbix](../Screenshot/37-2_zabbix.png)

- Enregistrer une regle dans OPNsense sur VLAN 10 pour autoriser le trafic SNMP  vers VLAN 20


|Protocol|	Source	|Port|	Destination	|Port	|Gateway|	Schedule	|	Description|
|--------|-----------|---|---------------|-------|------|-------------|---------------|
|	IPv4 UDP|	10.10.10.40|	*|	This Firewall|	161 (SNMP)|	*	|*	|	Allow zabbix SNMP to VLAN20|

### 14.4.2 Intégration dans Zabbix

- Créer le groupe : Zabbix -> Data collections -> Host Group -> Create host group :  Infra-Firewall
- Créer le Hosts : Zabbix -> Data collections -> Hosts -> Create Host
![zabbix](../Screenshot/37-3_zabbix.png)


### 14.5 Configuration des notifications Telegram

- Créer le bot sur Telegram et récupérer les informations : bot token + chat ID
- Zabbix -> Media type -> sélectionner Telegram -> dans les paramètres :
    ```
    api_token = <BOT_TOKEN>
    api_chat_id = {ALERT.SENDTO}
    api_parse_mode = Markdown
    ```

- Zabbix -> Users -> user -> onglet media -> dans le champ "Send to" : entrer chat ID et sélectionner les niveaux d'alertes.

- Tester en désactivant l'agent zabbix sur le conteneur ct-dns, l'alerte Telegram  se déclenche.


![alt text](../Screenshot/37-4_zabbix_telegram.png)


> Note :  Quand l'incident est résolu  une nouvelle notification est reçue

- Faire un backup du conteneur et de la vm OPNsense

# 15. Création de la DMZ


## 15.1 Création de l'interface DMZ dans proxmox

![alt text](../Screenshot/38-1_Traefik.png)

--> __Apply configuration__

## 15.2 Ajout à OPNsense pour création de la DMZ

- Node -> vm OPNsense -> Hardware -> ajouter l'interface créée. 

![alt text](../Screenshot/38-2_Traefik.png)

- OPNsense -> Assignements -> Add

- OPNsense -> Interfaces -> DMZ -> Configurer l'IP (/24 = réseau)


# 16.  Vm Traefik en DMZ

## 16.1 Création de la VM

- Créer le dataset pour la vm

```bash 
    zfs create tank/vmdata/vm-traefik
    zfs set compression=lz4 tank/vmdata/vm-traefik
    zfs set atime=off tank/vmdata/vm-traefik
```

- Créer la vm debian 12

![alt text](../Screenshot/38-3_Traefik.png)

- Installer Debian 12 et attribuer l'ip statique 172.16.100.100/24 (vmbr0 sans tag VLAN)
- DNS = interface DMZ du firewall



## 16.2 Règles OPNsense pour l'interface DMZ

- Ajouter :
  

|Protocol |	Source	| Port	| Destination| Port |	Gateway |	Schedule	|	Description |
|---------|--------|----------|----------|-----|----------|----------------|-----------------|
|IPv4 TCP/UDP |	172.16.100.100|	*| This Firewall|	 (53)|	*	| * |	Allow DNS |	   
|IPv4 TCP|	DMZ net|	*|	*|	HTTP (80)|	*|	*	|	Allow HTTP	|   
|IPv4 TCP|	DMZ net|	*|	*|	HTTPS (443)|	*|	*|		Allow HTTPS|	   
|IPv4 ICMP|	172.16.100.100/32|	*|	*|	*|	*|	*|	Allow ICMP|
| IPv4 *|	DMZ net|	*|	*|	*|	*|	*|		Block all DMZ out|


## 16.3 Règle NAT Outbound pour la DMZ

- Ajouter :
  
|Interface	|Source	|Source Port	|Destination	|Destination Port|	NAT Address|	NAT Port|	Static Port|	Description|
|----|----|----|-----|------|------|-------|------|-------|
|VLAN5_WAN|	DMZ net|	*|	*|	*|	Interface address|	*|	NO|	NAT DMZ|


## 16.4 Tests et mise à jours

- Tester la connection avec ping entre et depuis la vm et le firewall
- Tester la résolution DNS
- Faire la mise à jours de la vm (si besoin modifier /etc/apt/sources.list)


 
# 17. PBS (à venir)


# 18. Sauvegardes 3-2-1 (À venir)


# 19. LXC apps (À venir)


# 20. VPS + Pangolin

        










