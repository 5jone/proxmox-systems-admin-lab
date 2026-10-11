# Server Inventory

This public inventory records deployed systems and roles without exposing exact live IP addresses or other unnecessary environment identifiers.

| Hostname | Operating System | Role | Network | Addressing | Status |
| --- | --- | --- | --- | --- | --- |
| CORP-DC01 | Windows Server 2022 Standard Desktop Experience | Planned first Domain Controller and DNS server | EOVLAN / VLAN 152 | Reserved static IPv4 | Base OS and networking validated |

## CORP-DC01 Notes

- Hypervisor: Proxmox VE
- Network adapter model: VirtIO
- Guest VLAN tag: blank because VLAN 152 is already mapped to EOVLAN
- Gateway: existing infrastructure gateway
- DNS: existing bootstrap resolver until AD-integrated DNS is deployed
- Next role milestone: Active Directory Domain Services and DNS promotion
