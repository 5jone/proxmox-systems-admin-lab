# Server Inventory

This public inventory records deployed systems and roles without exposing exact live IP addresses or other unnecessary environment identifiers.

| Hostname | Operating System | Role | Network | Addressing | Status |
| --- | --- | --- | --- | --- | --- |
| CORP-DC01 | Windows Server 2022 Standard Desktop Experience | First AD DS Domain Controller and authoritative DNS server | EOVLAN / VLAN 152 | Reserved static IPv4 | Promoted and validated |
| CORP-DC02 | Windows Server 2022 Standard Desktop Experience | Replica AD DS Domain Controller, DNS server, and Global Catalog | EOVLAN / VLAN 152 | Reserved static IPv4 | Promoted and validated |
| CORP-NTP01 | Rocky Linux 9 planned | Internal NTP server using chrony | EOVLAN / VLAN 152 | Reserved static IPv4 planned | Next |
| CORP-NTP02 | Rocky Linux 9 planned | Redundant internal NTP server using chrony | EOVLAN / VLAN 152 | Reserved static IPv4 planned | Next |

## CORP-DC01 Notes

- Hypervisor: Proxmox VE
- CPU: 2 virtual cores
- Memory: 8 GiB fixed
- Memory ballooning: disabled
- Network adapter model: VirtIO
- Guest VLAN tag: blank because VLAN 152 is already mapped to EOVLAN
- Gateway: existing infrastructure gateway
- DNS client: local CORP-DC01 DNS service
- DNS forwarding: approved existing infrastructure resolver
- Forest / domain: `corp.example.test`
- NetBIOS name: `CORP`
- Current FSMO validation: CORP-DC01 holds the PDC Emulator role
- Next role milestone: deploy and promote CORP-DC02 as a replica Domain Controller and DNS server

## CORP-DC02 Notes

- Hypervisor: Proxmox VE
- Machine: q35
- Firmware: OVMF / UEFI
- CPU: 2 virtual cores using host CPU type
- Memory: 8 GiB fixed
- Memory ballooning: disabled
- Disk controller: VirtIO SCSI Single
- System disk: 64 GiB qcow2
- Network adapter model: VirtIO
- Guest VLAN tag: blank because VLAN 152 is already mapped to EOVLAN
- AD DS role: additional Domain Controller in `corp.example.test`
- DNS role: Active Directory-integrated DNS
- Global Catalog: enabled
- Replication: validated with zero failures in the replication summary
- SYSVOL / NETLOGON: published
- DNS-client order: prefers CORP-DC01, then itself
- Time: follows the Active Directory domain hierarchy through the forest-root PDC Emulator
- Proxmox HA: not enabled

## CORP-NTP01 / CORP-NTP02 Planned Notes

- Operating system: Rocky Linux 9
- Service: chrony / NTP
- Design: two independent internal time servers
- Upstream: approved NIST public time service
- Consumers: forest-root PDC Emulator uses the internal pair; non-PDC Windows systems remain on the Active Directory domain hierarchy
- Directory dependency: time servers remain standalone so internal time can remain available during directory-authentication trouble
- Next validation: upstream reachability, chrony source health, PDC source selection, and one-server failover

