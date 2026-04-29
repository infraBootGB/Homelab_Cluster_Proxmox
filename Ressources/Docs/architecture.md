# Architecture - Homelab Cluster Proxmox

## Matériel

| Nœud | Machine | Rôle | RAM | Stockage |
|------|---------|------|-----|----------|
| proxmox1 | Dell OptiPlex | Quorum uniquement | 32Go | SSD 250Go (système) |
| proxmox2 | Ryzen 7 | Services / Apps | 32Go | NVMe 2To (tank) · NVMe 1To (futur Nextcloud) · SSD 1To (système) |
| proxmox3 | Ryzen 7 | Infra / PBS | 32Go | NVMe 2To (tank) · NVMe 500Go (PBS passthrough) · SSD 1To (système) |

---

## Segmentation réseau (VLANs)

| VLAN | Réseau | Rôle |
|------|--------|------|
| VLAN 10 | 10.10.10.0/24 | Services Infra (AdGuard, Zabbix, PBS, vm-web) |
| VLAN 20 | 10.10.20.0/24 | Management (nodes Proxmox, OPNsense, switch) |
| VLAN 30 | 10.10.30.0/24 | CrowdSec (agents nodes -> LAPI OPNsense) |
| VLAN 40 | 10.10.40.0/24 | Services Web exposés (vm-web Docker) |
| VLAN 99 | 10.10.99.0/24 | Corosync (trafic cluster Proxmox isolé) |

> Plan d'adressage :  [configuration_reseau.md](./configuration_reseau.md)

---

## Services par nœud

### Node 3 — Proxmox3 (Infra)

| Service | Type | IP | VLAN |
|---------|------|----|------|
| vm-OPNsense | VM | 10.10.20.1 (MGMT) | 10/20/30/40 |
| ct-dns (AdGuard Home) | LXC | 10.10.10.50 | 10 |
| ct-zabbix | LXC | 10.10.10.40 | 10 |
| vm-pbs (PBS) | VM | 10.10.10.60 | 10 |

### Node 2 — Proxmox2 (Apps)

| Service | Type | IP | VLAN |
|---------|------|----|------|
| vm-web | VM | 10.10.40.100 | 40 |

**Containers Docker dans vm-web :**
- Newt (tunnel WireGuard vers VPS)
- Wiki.js + PostgreSQL
- Uptime Kuma

---

## Pare-feu et sécurité

### OPNsense (vm-opnsense — node 3)



- **Suricata IDS** : surveillance passive WAN, alertes sur l'interface OPNsense
- **CrowdSec plugin** : LAPI écoutant sur `10.10.30.1:8080`, bouncer  actif
- **Intégration Suricata → CrowdSec** : `eve.json` lu par CrowdSec-> décisions de blocage via bouncer
- **Tailscale plugin** : accès distant sécurisé à l'infrastructure (routes VLAN 10 + 20 déclarés en subnet routes)
- **Kea DHCP** + **Unbound DNS** (forwarding vers AdGuard)


## Exposition des services (VPS + Pangolin)




- Chaque service exposé correspond à une ressource dans Pangolin

---

## DNS

```
Clients LAN
   │
   ▼
OPNsense / Unbound (10.10.x.1)
   │ forwarding
   ▼
AdGuard Home (10.10.10.50) — filtrage, listes de blocage, DNSSEC
   │ DoH
   ▼
Quad9 (9.9.9.9)
```

---

## Stockage et sauvegarde (stratégie 3-2-1)

### Pools ZFS

| Nœud | Pool | Disque | Capacité |
|------|------|--------|----------|
| proxmox2 | tank | NVMe 2To | ~1.81To utiles |
| proxmox3 | tank | NVMe 2To | ~1.81To utiles |

Réplication ZFS bidirectionnelle entre node 2 et node 3 (toutes les 15 minutes via Proxmox).

### Stratégie 3-2-1


- Copie 1 -> données live (tank node 2 ou node 3)
- Copie 2 -> réplication ZFS inter-nœuds (node 2 / node 3)
- Copie 3 ->PBS quotidien (node 3) → sync Backblaze B2 (off-site, à venir)


### PBS (Proxmox Backup Server)

- VM Debian sur node 3 (VLAN 10)
- Datastore sur NVMe 500Go en passthrough
- Backup quotidien à 3h00 de toutes les VMs/CTs
- Rétention : semaine complète + dernier mois + 3 derniers mois
- Restauration testée

> Procédure : [install.md section 19](./install.md#19-pbs---proxmox-backup-server)

---

## Monitoring

| Outil | Rôle | Accès |
|-------|------|-------|
| Zabbix (ct-zabbix) | Supervision agents nodes + OPNsense SNMP + VPS ICMP | 10.10.10.40 |
| Uptime Kuma (vm-web) | Disponibilité des services exposés via Pangolin | Via Pangolin |
| Alertes Telegram | Notifications Zabbix (incidents + résolution) et Uptime Kuma | - |

---

## Accès distant

| Méthode | Usage |
|---------|-------|
| Tailscale | Administration quotidienne (Proxmox UI, OPNsense, AdGuard, Zabbix) |
| Pangolin | Accès aux services exposés publiquement |
| SSH | Administration système |

---

## Liens vers la documentation

| Sujet | Fichier |
|-------|---------|
| Procédure d'installation complète | [install.md](./install.md) |
| Plan d'adressage réseau et switch | [configuration_reseau.md](./configuration_reseau.md) |
| Matériel  | [hardware.md](./hardware.md) |
