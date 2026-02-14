
# Homelab Cluster Proxmox

<div align="center">

[![Proxmox VE 9](https://img.shields.io/badge/Proxmox%20VE-9-orange?style=flat-square&logo=proxmox)](https://www.proxmox.com) [![OPNsense 25.7](https://img.shields.io/badge/OPNsense-25.7-blue?style=flat-square&logo=opnsense)](https://opnsense.org) [![CrowdSec Centralized](https://img.shields.io/badge/CrowdSec-Centralized-green?style=flat-square&logo=crowdsec)](https://crowdsec.net) [![ZFS Local Mirrors](https://img.shields.io/badge/ZFS-Local%20Mirrors-lightgrey?style=flat-square&logo=zfs)](https://openzfs.org) [![Tailscale Zero Trust](https://img.shields.io/badge/Tailscale-Zero%20Trust-purple?style=flat-square&logo=tailscale)](https://tailscale.com) [![Docker Containers](https://img.shields.io/badge/Docker-Containers-blue?style=flat-square&logo=docker)](https://www.docker.com)

</div>


Homelab réalisé dans le cadre de ma reconversion vers l’administration systèmes et réseaux, après l’obtention du titre TSSR. Ce projet est en cours de réalisation.




## 📚 Sommaire

- [Homelab Cluster Proxmox](#homelab-cluster-proxmox)
  - [📚 Sommaire](#-sommaire)
  - [🎯 Objectifs techniques](#-objectifs-techniques)
  - [🏛️ Résumé d'architecture](#️-résumé-darchitecture)
  - [🌐 Schéma de l’infrastructure](#-schéma-de-linfrastructure)
  - [🛠️ Compétences mobilisées](#️-compétences-mobilisées)
  - [⚙️ Choix technologiques](#️-choix-technologiques)
  - [📂 Documentation technique](#-documentation-technique)





## 🎯 Objectifs techniques

- Déployer un cluster Proxmox 3 noeuds 
- Mettre en place segmentation réseau VLAN
- Sécuriser l’infrastructure 
- Exposition de service via HAProxy ( à venir)
- Mettre en place monitoring
- Mettre en place les sauvegardes (à venir)


## 🏛️ Résumé d'architecture

- Virtualisation : Cluster Proxmox 3 noeuds (HA manager + réplication ZFS)
- Stockage : ZFS mirror + réplication inter-nodes (bidirectionnelle par datasets)
- VLAN 5 : WAN
- VLAN 10 : Service infra + apps internes
- VLAN 20 : Management Proxmox + UI switch
- VLAN 30 : Communication CrowdSec (Agents -> LAPI sur OPNsense)
- VLAN 99 : Corosync isolé
- VLAN 100 : Web : services publiés via HAProxy 
- VLAN 4094 : Blackhole
- DNS interne : Unbound -> AdGuard -> DoH (Quad9)
- Sécurité : OPNsense + IDS (Natif OPNsense basé sur Suricata) + CrowdSec
- Sauvegardes : PBS + stratégie 3-2-1 
- Accès distant sécurisé : Tailscale


## 🌐 Schéma de l’infrastructure

![schema_infrastrucure](Ressources/Docs/Schema_infrastructure.drawio.svg)

## 🛠️ Compétences mobilisées


- Mise en place d’un cluster Proxmox (VM, LXC, HA basique)

- Utilisation de ZFS (mirror, réplication, snapshots)

- Segmentation réseau par VLAN et configuration de switch manageable

- Déploiement d’un firewall OPNsense 

- Supervision avec Zabbix

- Sauvegardes avec Proxmox Backup Server

- Mise en place de DNS interne avec filtrage 

- Sécurisation des accès (SSH par clés, Tailscale, CrowdSec)

- Documentation et tests 

## ⚙️ Choix technologiques

- __Proxmox__ : Solution open-source basée sur Debian et adaptable à un matériel hétérogène

- __ZFS__ : Pour tester intégrité des données, snapshots et réplication

- __OPNsense__ : Firewall open-source communautaire choisi pour découvrir une alternative à pfSense. Utilisation des plugins

- __CrowdSec__ : Choisi pour l'aspect moderne et communautaire

- __AdGuard Home__ : Solution moderne pour le filtrage DNS
  
- __Zabbix__ : Logiciel open-source reconnu 

- __PBS__ : Utilisation d'un vrai logiciel de sauvegardes et tests de restauration

- __Tailscale__ : Accès distant moderne et sécurisé au lab



## 📂 Documentation technique

- [Configuration réseau](Ressources/Docs/configuration_reseau.md)
- [Guide d'installation](Ressources/Docs/install.md)
- [Hardware](Ressources/Docs/hardware.md) 


