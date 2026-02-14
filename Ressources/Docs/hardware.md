
# Hardware

## **Noeuds Proxmox VE 9** 



| Élément | Nom | Processeur  | RAM | Réseau | Stockage | Rôle |
|----------|---|---------|-----|----------|-------|-------|
| Node 1 | Proxmox1 |  (Dell i5) Optiplex 7050 | 32 Go | 1 Gbps |  SSD 250 Go (Système) |  Quorum cluster  |
| Node 2 | Proxmox2     |  Ryzen 7      |  32 Go|  2,5 Gbps |  NVMe 2x2 To - Mirror ZFS ~2To utiles   |  Support services Apps <br>  Réplication ZFS vers node 2 |
| -| -| -| -|- | SSD 4 To  - ZFS |  Cold storage (Sauvegardes) |
| -| - | -| -| - | SSD externe 1 To |  Système |
| Node 3 | Proxmox3     |  Ryzen 7      |  32 Go|  2,5 Gbps |  NVMe 500 Go + 1 To <BR> Mirror ZFS ~500 Go utiles |  Support services infra : vm OPNsense, ct-sec-dns, ct-zabbix, ct-pbs <br>  Réplication ZFS vers node 3|
| -| - | -| -|  SSD externe 1 TO |  Système |



  ## **Réseau**

- Switch manageable 2.5 Gbps 8 ports
- Accès Internet 2.5 Gbps
- DMZ (Box internet) redirigée vers le firewall OPNsense