# CORP-DC02

## Status

CORP-DC02 has been provisioned as the second Windows Server virtual machine for the corporate Active Directory environment.

Virtual hardware and network placement are complete. Windows Server 2022 installation, guest driver installation, static addressing, domain discovery, and replica Domain Controller promotion remain pending.

> Public portfolio note: exact IP addresses, MAC addresses, VM identifiers, gateway/resolver addresses, credentials, and other live environment identifiers are intentionally omitted.

## Planned Role

- Operating System: Windows Server 2022 Standard Desktop Experience
- Planned role: Replica Active Directory Domain Services (AD DS) Domain Controller
- Planned DNS role: Active Directory-integrated DNS
- Forest / domain: `corp.example.test`
- Network: EOVLAN
- VLAN: 152
- Addressing: Reserved static IPv4 after operating system installation
- Pre-promotion DNS client: CORP-DC01

## Virtual Hardware

- Machine: q35
- Firmware: OVMF / UEFI
- EFI disk: enabled
- TPM: not required for this lab role
- CPU: 2 virtual cores
- CPU type: host
- vCPU architecture: left at the Proxmox default because the delegated account cannot explicitly set the architecture
- Memory: 8 GiB fixed
- Memory ballooning: disabled
- KSM: not explicitly enabled for the guest
- Disk controller: VirtIO SCSI Single
- System disk: 64 GiB
- Disk format: qcow2
- I/O thread: enabled
- Cache: Proxmox default / no cache
- Discard: disabled pending confirmed backing-storage support
- SSD emulation: disabled because SSD semantics have not been verified for the backing storage
- Network adapter: VirtIO
- Network attachment: EOVLAN
- Guest VLAN tag: blank by design
- Proxmox firewall flag on NIC: enabled
- QEMU Guest Agent integration: enabled on the Proxmox side
- Proxmox HA: not enabled

## Build Log

### 2026-11-11 — Virtual machine provisioning

- Created the CORP-DC02 virtual machine using the administrator-approved VLAN-based VM numbering convention.
- Selected q35 as the virtual chipset and OVMF / UEFI firmware with an EFI disk.
- Selected VirtIO SCSI Single for the virtual storage controller.
- Created a 64 GiB qcow2 system disk with I/O thread enabled.
- Kept SSD emulation and discard disabled because the backing storage capabilities have not been independently verified.
- Configured two virtual CPU cores using the host CPU type.
- Left the explicit vCPU architecture setting at the Proxmox default after confirming the delegated account is not permitted to set that field directly.
- Provisioned 8 GiB fixed memory with ballooning disabled, applying the resource lesson learned during CORP-DC01 patching.
- Left Kernel Same-page Merging (KSM) unselected for this guest.
- Attached a VirtIO NIC to EOVLAN with the guest VLAN tag blank because VLAN membership is supplied by the VNet.
- Kept the Proxmox firewall flag enabled on the NIC.
- Enabled QEMU Guest Agent integration on the Proxmox side; the Windows guest service will be installed after the operating system is available.
- Left Proxmox High Availability (HA) disabled because the current design provides directory-service redundancy, not multi-node hypervisor failover.

## Validation

- [x] CORP-DC02 VM object created.
- [x] q35 machine type selected.
- [x] OVMF / UEFI firmware selected.
- [x] EFI disk present.
- [x] VirtIO SCSI Single selected.
- [x] 64 GiB qcow2 system disk provisioned.
- [x] Two virtual CPU cores configured with host CPU type.
- [x] 8 GiB fixed memory configured.
- [x] Memory ballooning disabled.
- [x] VirtIO NIC attached to EOVLAN.
- [x] Guest VLAN tag left blank by design.
- [x] Proxmox firewall flag enabled on the NIC.
- [x] QEMU Guest Agent integration enabled on the Proxmox side.
- [x] Proxmox HA left disabled.
- [ ] Windows Server 2022 installed.
- [ ] VirtIO guest drivers installed.
- [ ] QEMU Guest Agent service installed and running inside Windows.
- [ ] Hostname confirmed as CORP-DC02.
- [ ] Reserved static IPv4 configuration applied.
- [ ] CORP-DC01 configured as the DNS client before promotion.
- [ ] Active Directory SRV discovery validated.
- [ ] AD DS role installed.
- [ ] CORP-DC02 promoted as an additional Domain Controller.
- [ ] AD DS and DNS replication validated between CORP-DC01 and CORP-DC02.

## Why 8 GiB is the initial baseline

CORP-DC01 demonstrated that 4 GiB was functional but unnecessarily tight during cumulative Windows servicing. Because host capacity is available, CORP-DC02 starts with 8 GiB fixed memory to provide more predictable headroom for Windows Server, patching, AD DS, DNS, and management tools.

## Why Proxmox HA is not enabled

A second Domain Controller improves Active Directory and DNS service redundancy, but both Domain Controllers still share the same Proxmox host failure domain.

Proxmox HA is a separate hypervisor-cluster capability intended to restart workloads on another node after a host failure. That is not part of the current single-node lab design.

## Next

- Install Windows Server 2022 Standard with Desktop Experience.
- Load the matching VirtIO SCSI driver if Windows Setup does not initially see the virtual disk.
- Install the VirtIO network driver and QEMU Guest Agent inside Windows.
- Verify Device Manager is clean.
- Rename the server to CORP-DC02 if the final hostname is not already applied.
- Patch Windows fully before AD DS promotion.
- Apply the reserved static IPv4 configuration.
- Point CORP-DC02 DNS to CORP-DC01.
- Verify Active Directory SRV records resolve before installing and promoting AD DS.
