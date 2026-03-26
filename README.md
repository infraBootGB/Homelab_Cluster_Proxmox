# Homelab Cluster Proxmox

<div align="center">

[![Proxmox VE 9](https://img.shields.io/badge/Proxmox%20VE-9-orange?style=flat-square&logo=proxmox)](https://www.proxmox.com) [![OPNsense 25.7](https://img.shields.io/badge/OPNsense-25.7-blue?style=flat-square&logo=opnsense)](https://opnsense.org) [![CrowdSec Centralized](https://img.shields.io/badge/CrowdSec-Centralized-green?style=flat-square&logo=crowdsec)](https://crowdsec.net) [![ZFS Local Mirrors](https://img.shields.io/badge/ZFS-Local%20Mirrors-lightgrey?style=flat-square&logo=zfs)](https://openzfs.org) [![Tailscale Zero Trust](https://img.shields.io/badge/Tailscale-Zero%20Trust-purple?style=flat-square&logo=tailscale)](https://tailscale.com) [![Docker Containers](https://img.shields.io/badge/Docker-Containers-blue?style=flat-square&logo=docker)](https://www.docker.com)

</div>

Homelab conçu et maintenu en autonomie dans le cadre d'une reconversion vers l'administration systèmes et réseaux. Infrastructure en production, documentée comme en contexte professionnel.

## 📚 Sommaire

- [Homelab Cluster Proxmox](#homelab-cluster-proxmox)
  - [📚 Sommaire](#-sommaire)
  - [🎯 Objectifs techniques](#-objectifs-techniques)
  - [🏛️ Résumé d'architecture](#️-résumé-darchitecture)
  - [🌐 Schéma de l'infrastructure](#-schéma-de-linfrastructure)
  - [🛠️ Compétences mobilisées](#️-compétences-mobilisées)
  - [⚙️ Choix technologiques](#️-choix-technologiques)
  - [📂 Documentation technique](#-documentation-technique)

## 🎯 Objectifs techniques

- Déployer un cluster Proxmox 3 nœuds en haute disponibilité
- Mettre en place une segmentation réseau par VLAN
- Sécuriser l'infrastructure (firewall, IDS, CrowdSec, Zero Trust)
- Exposer des services via reverse proxy (évolution en cours : VPS + Pangolin)
- Mettre en place la supervision et les alertes
- Mettre en place les sauvegardes (PBS — en cours)

## 🏛️ Résumé d'architecture

- Virtualisation : Cluster Proxmox 3 nœuds (HA Manager + réplication ZFS)
- Stockage : ZFS mirror + réplication inter-nœuds (bidirectionnelle par datasets)
- VLAN 5 : WAN
- VLAN 10 : Services infra + apps internes
- VLAN 20 : Management Proxmox + UI switch
- VLAN 30 : Communication CrowdSec (Agents → LAPI sur OPNsense)
- VLAN 99 : Corosync isolé
- VLAN 100 : DMZ (architecture en évolution vers VPS + Pangolin)
- VLAN 4094 : Blackhole
- DNS interne : Unbound → AdGuard Home → DoH (Quad9)
- Sécurité : OPNsense + IDS/IPS (Suricata) + CrowdSec centralisé
- Sauvegardes : PBS + stratégie 3-2-1 (en cours)
- Accès distant sécurisé : Tailscale

## 🌐 Schéma de l'infrastructure

> **Note :** Schéma et documentation en cours de mise à jour suite à l'évolution 
> de l'architecture : exposition des services (migration DMZ locale vers VPS + Pangolin).

![schema_infrastrucure](Ressources/Docs/Schema_infrastructure.drawio.svg)

## 🛠️ Compétences mobilisées

- Mise en place d'un cluster Proxmox (VM, LXC, HA Manager, Affinity Rules)
- Utilisation de ZFS (mirror, réplication, snapshots)
- Segmentation réseau par VLAN et configuration de switch manageable
- Déploiement et administration d'un firewall OPNsense
- Supervision avec Zabbix (agents, alertes Telegram)
- Sécurisation des accès (SSH par clés, Tailscale, CrowdSec)
- DNS interne avec filtrage (AdGuard Home, DoH)
- Déploiement de conteneurs Docker
- Documentation technique structurée

## ⚙️ Choix technologiques

- __Proxmox__ : Solution open-source basée sur Debian, adaptable à un matériel hétérogène
- __ZFS__ : Intégrité des données, snapshots et réplication inter-nœuds
- __OPNsense__ : Firewall open-source communautaire, alternative à pfSense — utilisation des plugins (CrowdSec, Suricata, Tailscale)
- __CrowdSec__ : Protection collaborative et moderne, LAPI centralisée sur OPNsense
- __AdGuard Home__ : Filtrage DNS avec DoH vers Quad9
- __Zabbix__ : Supervision open-source reconnue, alertes Telegram
- __Traefik__ : Reverse proxy moderne, routage dynamique, TLS automatique (Let's Encrypt)
- __Tailscale__ : Accès distant Zero Trust sans exposition de ports
- __PBS__ : Proxmox Backup Server — sauvegardes et tests de restauration
- __Docker__ : Déploiement de conteneurs applicatifs (Wiki.js, etc.)

## 📂 Documentation technique

- [Configuration réseau](Ressources/Docs/configuration_reseau.md)
- [Guide d'installation](Ressources/Docs/install.md)
- [Hardware](Ressources/Docs/hardware.md)