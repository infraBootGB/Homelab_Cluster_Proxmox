# Configuration réseau (finale)

- VLAN aware activé sur vmbr0


| Élément           | Nom | IP    | Gateway | VLAN / Bridge /Interface | Commentaire |
|-------------------|-----|--------------|----------|-----|--------------------|
| __Node 1__         | Proxmox1      | 10.10.20.101/24 | 10.10.20.1   | VLAN 20 (MGMT) <br> vmbr0 | Quorum  <br> Agent CrowdSec |
| __Node 2__        | Proxmox2      | 10.10.20.102/24  |  10.10.20.1  | VLAN 20 (MGMT) <br> vmbr0 | Services Apps <br> Agent CrowdSec   |
| __Node 3__        | Proxmox3      | 10.10.20.103/24  | 10.10.20.1    |VLAN 20 (MGMT) <br> vmbr0 | Services Infra <br> Agent CrowdSec  |
| __Node 1__         | proxmox1-cor.lab | 172.16.99.10/24  |  -  | VLAN  99 (Corosync) <br> Linux VLAN "VLAN99" slave bridge <br> vmbr1| Trafic Corosync isolé |
| __Node 2__         | proxmox2-cor.lab     | 172.16.99.20/24  |  -  |   VLAN Tag 99 (Corosync) <br> Linux VLAN "VLAN99" slave bridge <br> vmbr1 | Trafic Corosync isolé |
| __Node 3__         | proxmox3-cor.lab     | 172.16.99.30/24  |   - |  VLAN Tag 99 (Corosync) <br> Linux VLAN "VLAN99" slave bridge <br> vmbr1| Trafic Corosync isolé |
| __Node 1__         | Proxmox1      | 10.10.30.101/24 | -    | VLAN 30 (CrowdSec) <br> vmbr0 | Communication agents CrowdSec nodes vers plugin Crowdsec sur OPNsense |
| __Node 2__        | Proxmox2      | 10.10.30.102/24  | -  |  VLAN 30 (CrowdSec)  <br> vmbr0 | Communication agents CrowdSec nodes vers plugin Crowdsec sur OPNsense |
| __Node 3__        | Proxmox3      | 10.10.30.103/24  | -   | VLAN 30 (CrowdSec) <br> vmbr0 | Communication agents CrowdSec nodes vers plugin Crowdsec sur OPNsense |
| __Switch__ | Switch | 10.10.20.111 | 10.10.20.1| - |UI switch <br> 802.1Q  Tags VLANs 5,10,20,30,99,100 |
| __Firewall__ | vm-OPNsense | Voir interfaces|- |-  | Firewall central|
| *Interface* | vtnet0| 192.168.1.210/24 <br> __WAN__ |  - | VLAN 5 (WAN)<br> vmbr0 | vmbr0 : VLAN aware <br> OPNsense 25.7 <br> Crowdsec (Plugin) <br> Suricata (Plugin) <br> Tailscale (Plugin) <br> Agent Qemu (Plugin) <br> HA Proxy|
| *Interface*| vtnet1| 10.10.10.1/24 (LAN) __Services__ | -  |VLAN 10 (Services) <br> vmbr0 | Interface LAN OPNsense <br> Services infra + apps non exposées |
|*Interface* |vtnet3 | 10.10.20.1/24 __MGMT__ | -  |VLAN 20 (MGMT) <br> vmbr0 | Management Proxmox <br> + Management du switch| 
|*Interface* |vtnet2 | 10.10.30.1/24 __CrowdSec__ | - | opt2   VLAN 30 (CrowdSec) <br> vmbr0 | opt2 - Communication agents CrowdSec nodes vers plugin CrowdSec sur OPNsense    |
|*Interface* | vtnet4| 172.16.100.1/24 __Web__ | -  |VLAN 100 (Web) <br> vmbr0 |Interface Web OPNsense <br> Services exposés via HAProxy|
|*Interface* | tailscale0|  - |    -  |opt1 <br> (Tailscale_Remote) | opt1 - Accès nodes via Tailscale 
| __LXC Web__ |ct-web | 172.16.100.100/24 Web|  172.16.100.1   |VLAN 100 (Web) <br> vmbr0 | Apps exposées |
| __LXC DNS__ | ct-dns    | 10.10.10.50/24  | 10.10.10.1   | VLAN 10 <br> vmbr0  | AdGuardHome |
| __LXC Zabbix__ | ct-zabbix| 10.10.10.40/24  |  10.10.10.1   | VLAN 10 <br> vmbr0 | Zabbix 
| __LXC Apps__ | ct-apps     | 10.10.10.30/24  |  10.10.10.1  |VLAN 10 <br> vmbr0 | Docker <br> Wiki.js <br> etc..|
| __PBS__  | vm-pbs  | 10.10.10.60/24 | 10.10.10.1 |VLAN 10 <br> vmbr0  | Proxmox Backup Server | 


---

# Configuration switch (finale)

## VLAN 802.1Q

| VLAN | Nom        | Ports  |Tagged |	Untagged |
|------|------------|--------|-------|----------|
|VLAN 5 |WAN        |1,2,3,4 |2,3,4  |  1       | 
|VLAN  10| Services       | 2,3,4  |2,3,4  |          | 
|VLAN 20 |MGMT      | 2,3,4,8|2,3,4  |  8       | 
|VLAN 30 |CrowdSec  | 2,3,4  |2,3,4  |          |
|VLAN 99 |Corosync  | 2,3,4  | 2,3,4 |          | 
|VLAN 100|Web       | 2,3,4  | 2,3,4 |          | 
|VLAN 4094|Blackhole| 2,3,4     | - |   2,3,4 |
- Port 8 = PC configurations/tests  

## VID 802.1Q (définit VLAN natif untagged)

| Port    |	PVID    |	Trames acceptées |
|---------|---------|-------------------|
|Port 1	  | 5       |	Untag-only (Isolation WAN)|
|Port 2   |	4094	    |   Tag-only|
|Port 3	  | 4094	    |    Tag-only|
|Port 4	  | 4094	    |    Tag-only|
|Port 8	  | 20	    |   Untag-only|