# Server Inventory

This public inventory records deployed systems and roles without exposing exact live IP addresses or other unnecessary environment identifiers.

| Hostname | Operating System | Role | Network | Addressing | Status |
| --- | --- | --- | --- | --- | --- |
| CORP-DC01 | Windows Server 2022 Standard Desktop Experience | First AD DS Domain Controller and authoritative DNS server | EOVLAN / VLAN 152 | Reserved static IPv4 | Promoted and validated |
| CORP-DC02 | Windows Server 2022 planned | Planned replica Domain Controller and DNS server | EOVLAN / VLAN 152 | Reserved static IPv4 planned | VM provisioned; OS installation pending |

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

## CORP-DC02 Provisioning Notes

- Hypervisor: Proxmox VE
- Machine: q35
- Firmware: OVMF / UEFI
- CPU: 2 virtual cores using host CPU type
- Memory: 8 GiB fixed from initial deployment
- Memory ballooning: disabled
- Disk controller: VirtIO SCSI Single
- System disk: 64 GiB qcow2
- I/O thread: enabled
- Network adapter model: VirtIO
- Guest VLAN tag: blank because VLAN 152 is already mapped to EOVLAN
- Proxmox firewall flag on NIC: enabled
- QEMU Guest Agent integration: enabled on the Proxmox side; guest service installation follows the Windows installation
- HA: not enabled; directory redundancy and Proxmox HA are separate concerns
- Current state: virtual hardware provisioned; Windows Server 2022 installation is next
