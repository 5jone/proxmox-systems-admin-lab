# CORP-DC01

## Contents

- [Status](#status)
- [Platform](#platform)
- [Virtual Hardware](#virtual-hardware)
- [Build Log](#build-log)
- [Validation](#validation)
- [Technical Notes](#technical-notes)
- [Evidence](#evidence)
- [Next](#next)

## Status

Base operating system and primary network configuration are complete. CORP-DC01 is statically addressed on the assigned lab network and connectivity has been validated.

Active Directory Domain Services (AD DS) and Domain Name System (DNS) promotion are the next major milestones.

## Platform

- Hypervisor: Proxmox VE
- Operating System: Windows Server 2022 Standard Desktop Experience
- Planned Role: First Domain Controller and internal DNS server
- Network: EOVLAN
- VLAN: 152
- Addressing: Reserved static IPv4 address on the assigned private /24 subnet
- Gateway: Existing infrastructure gateway
- Bootstrap DNS: Existing infrastructure resolver
- Guest VLAN tag: Blank by design

> Public portfolio note: exact IP addresses, MAC addresses, and other live environment identifiers are intentionally omitted.

## Virtual Hardware

- Machine: q35
- Firmware: OVMF / UEFI
- Disk Controller: VirtIO SCSI Single
- System Disk: 64 GB
- Disk Format: qcow2
- Network Adapter: VirtIO
- Proxmox NIC: `net0`
- Proxmox firewall flag on NIC: Enabled
- QEMU Guest Agent: Enabled

## Build Log

### 2026-10-04 — Base operating system deployment

- Created the virtual machine in Proxmox VE.
- Installed Windows Server 2022.
- Loaded VirtIO storage drivers.
- Installed the NetKVM network driver.
- Installed QEMU Guest Agent.
- Renamed the Windows host to `CORP-DC01`.
- Set the system disk first in the boot order.
- Ejected the Windows Server installation ISO.
- Verified successful boot from the virtual system disk.
- Left networking disconnected until the assigned lab network design was confirmed.

### 2026-10-10 — EOVLAN attachment and static addressing

- Attached Proxmox `net0` to the `EOVLAN` VNet using the VirtIO model.
- Left the guest VLAN tag blank because the environment administrator confirmed VLAN 152 is already mapped to EOVLAN.
- Confirmed Windows detected the `Red Hat VirtIO Ethernet Adapter`.
- Matched the virtual NIC identity between Proxmox and Windows to verify the correct guest adapter.
- Confirmed the adapter was operational inside Windows.
- Observed the initial infrastructure-provided DHCP configuration before making any changes.
- Disabled DHCP on the Windows Ethernet adapter.
- Confirmed the previous DHCP address was removed and Windows temporarily entered Automatic Private IP Addressing (APIPA), demonstrating there was no stale lease remaining.
- Applied the reserved static IPv4 configuration for CORP-DC01.
- Configured the existing infrastructure gateway and bootstrap DNS resolver.
- Verified the new static address reached Windows `AddressState = Preferred`.
- Verified external DNS resolution through the bootstrap resolver.
- Verified outbound HTTPS connectivity using a TCP port 443 test.

## Validation

- [x] Boots from the system disk.
- [x] Computer name is CORP-DC01.
- [x] Windows Server installation verified.
- [x] Device Manager is clean.
- [x] QEMU Guest Agent service is running.
- [x] VirtIO network adapter is detected by Windows.
- [x] Proxmox and Windows NIC identity match.
- [x] VM is attached to EOVLAN.
- [x] Guest VLAN tag is blank as required by the VNet mapping.
- [x] Reserved static IPv4 configuration is active.
- [x] Windows IPv4 address state is `Preferred`.
- [x] Default gateway path is configured.
- [x] Bootstrap DNS resolver is configured.
- [x] External DNS lookup succeeds.
- [x] Outbound TCP/443 connectivity succeeds.

## Technical Notes

### Why the guest VLAN tag is blank

The Proxmox `EOVLAN` VNet is already mapped to VLAN 152 by the environment administrator. The VM therefore attaches to the assigned Layer 2 network without adding a second guest-side VLAN tag.

This demonstrates an important distinction between:

- the VLAN identifier, which defines Layer 2 network membership, and
- the IPv4 subnet, which defines Layer 3 addressing.

### Why observe DHCP before moving to static addressing

The initial DHCP lease served as a useful connectivity check before changing the server configuration. It demonstrated that the VM could reach the assigned Layer 2 network and existing infrastructure services.

The Domain Controller requires a predictable address, so the temporary DHCP configuration was replaced with the reserved static configuration after connectivity was confirmed.

### Why the temporary APIPA state mattered

After DHCP was disabled, Windows temporarily self-assigned an Automatic Private IP Addressing (APIPA) address.

That state was useful because it confirmed the old DHCP lease was no longer present before the reserved static address was applied.

### DNS transition

CORP-DC01 currently uses the existing infrastructure DNS resolver only for bootstrap.

Immediately before Domain Controller promotion, its DNS client configuration will transition to the server's own internal DNS service. External name resolution will then be handled through DNS forwarding instead of assigning a public DNS resolver directly to the Domain Controller.

## Evidence

See `/screenshots/CORP-DC01/`.

Evidence for this milestone should demonstrate:

- Proxmox NIC attached to EOVLAN
- Windows VirtIO adapter present
- matching NIC identity between hypervisor and guest
- static IPv4 configuration active
- `AddressState = Preferred`
- successful DNS resolution
- successful TCP port 443 connectivity

Any screenshots used in the public repository should redact exact IP addresses, MAC addresses, usernames, or other environment-specific identifiers when they are not necessary to demonstrate the technical result.

## Next

- Patch Windows Server as required.
- Transition CORP-DC01 from the bootstrap DNS resolver to its own DNS service immediately before promotion.
- Install Active Directory Domain Services (AD DS) and DNS.
- Promote CORP-DC01 as the first Domain Controller for the lab forest.
- Configure approved DNS forwarding for external name resolution.
