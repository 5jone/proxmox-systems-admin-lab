# Server Inventory

This public inventory records deployed systems and roles without exposing exact live IP addresses or other unnecessary environment identifiers.

| Hostname | Operating System | Role | Network | Addressing | Status |
| --- | --- | --- | --- | --- | --- |
| CORP-DC01 | Windows Server 2022 Standard Desktop Experience | First AD DS Domain Controller and authoritative DNS server | EOVLAN / VLAN 152 | Reserved static IPv4 | Promoted and validated |

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
