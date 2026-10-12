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

CORP-DC01 is online as the first Active Directory Domain Services (AD DS) Domain Controller and authoritative Domain Name System (DNS) server for the lab forest.

The server is statically addressed on the assigned lab network, patched, promoted, and validated for both internal Active Directory service discovery and external DNS forwarding.

## Platform

- Hypervisor: Proxmox VE
- Operating System: Windows Server 2022 Standard Desktop Experience
- Current Role: First Domain Controller and authoritative DNS server
- Forest / Domain: `corp.example.test`
- NetBIOS Name: `CORP`
- Network: EOVLAN
- VLAN: 152
- Addressing: Reserved static IPv4 address on the assigned private /24 subnet
- Gateway: Existing infrastructure gateway
- DNS client: Local CORP-DC01 DNS service
- External DNS resolution: Forwarded to the approved infrastructure resolver
- Guest VLAN tag: Blank by design

> Public portfolio note: exact IP addresses, MAC addresses, credentials, and other live environment identifiers are intentionally omitted.

## Virtual Hardware

- Machine: q35
- Firmware: OVMF / UEFI
- CPU: 2 virtual cores
- Memory: 8 GiB fixed
- Memory ballooning: Disabled
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

### 2026-10-11 — Patching, resource adjustment, AD DS promotion, and DNS validation

- Completed Windows Update before directory promotion.
- Troubleshot a Windows servicing restart that became stuck while stopping the Update Orchestrator Service.
- Verified the Proxmox reboot task timed out and subsequent GUI power actions were blocked by a VM lock.
- Recovered the guest using the noVNC `Hard Stop` action after the earlier task released.
- Allowed Windows servicing to complete and confirmed updates finished successfully.
- Increased fixed VM memory from the initial 4 GiB lab baseline to 8 GiB after observing memory pressure during cumulative update servicing.
- Kept memory ballooning disabled for predictable infrastructure-server memory availability.
- Confirmed Windows recognized approximately 8 GiB of physical memory.
- Changed the server's DNS client from the bootstrap resolver to its own reserved static address immediately before promotion.
- Installed the AD DS role and management tools using PowerShell.
- Created a separate Directory Services Restore Mode (DSRM) password and stored it privately.
- Created the new `corp.example.test` forest and root domain with NetBIOS name `CORP`.
- Installed DNS as part of forest promotion.
- Allowed the promotion-triggered reboot to complete.
- Verified the new domain reports the expected DNS root and Windows Server 2016 domain functional level.
- Verified CORP-DC01 holds the PDC Emulator role as the first Domain Controller.
- Configured the approved existing infrastructure resolver as a DNS forwarder.
- Verified the LDAP Domain Controller discovery SRV record resolves to CORP-DC01.
- Verified external DNS resolution succeeds through the configured forwarder.

## Validation

- [x] Boots from the system disk.
- [x] Computer name is CORP-DC01.
- [x] Windows Server installation verified.
- [x] Windows updates completed before promotion.
- [x] Guest memory increased to 8 GiB fixed and recognized by Windows.
- [x] Device Manager is clean.
- [x] QEMU Guest Agent service is running.
- [x] VirtIO network adapter is detected by Windows.
- [x] Proxmox and Windows NIC identity match.
- [x] VM is attached to EOVLAN.
- [x] Guest VLAN tag is blank as required by the VNet mapping.
- [x] Reserved static IPv4 configuration is active.
- [x] Windows IPv4 address state is `Preferred`.
- [x] Default gateway path is configured.
- [x] AD DS role installed successfully.
- [x] `corp.example.test` forest and root domain created.
- [x] CORP-DC01 promoted as the first Domain Controller.
- [x] DNS role installed with the forest.
- [x] CORP-DC01 uses its own internal DNS service.
- [x] DNS forwarder configured for approved upstream resolution.
- [x] LDAP Domain Controller discovery SRV record resolves successfully.
- [x] External DNS resolution succeeds through forwarding.
- [x] CORP-DC01 is reported as the PDC Emulator.

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

### Why memory was increased to 8 GiB

The server was originally provisioned with a conservative 4 GiB fixed-memory lab baseline. During cumulative update servicing, Windows consumed nearly all available guest memory and servicing became slow.

Because host capacity was available, the Domain Controller was resized to 8 GiB fixed memory before promotion. This provides additional operational headroom for Windows Server, AD DS, DNS, patching, management tools, and normal background services while preserving predictable memory availability.

### Why CORP-DC01 now points DNS at itself

Active Directory depends on internal DNS records for locating Domain Controllers and services such as LDAP and Kerberos.

Immediately before promotion, CORP-DC01's DNS client was changed from the temporary bootstrap resolver to its own internal DNS service. This keeps Active Directory service discovery inside the authoritative `corp.example.test` namespace.

### Why an external DNS forwarder is used

CORP-DC01 is authoritative for the lab's internal Active Directory namespace, but it is not authoritative for public Internet namespaces.

When CORP-DC01 receives a query it cannot answer locally, its DNS Server service forwards that query to the approved upstream resolver. This is different from configuring the Domain Controller's network adapter to use a public or external resolver directly.

### Why the SRV lookup matters

The query for the LDAP Domain Controller discovery SRV record verifies that Active Directory registered the service-location record clients use to discover a Domain Controller.

A successful SRV response identifying CORP-DC01 and LDAP over TCP port 389 is stronger evidence than merely confirming the server booted after promotion.

## Evidence

See `/screenshots/CORP-DC01/`.

Evidence for the current milestone should demonstrate:

- Proxmox NIC attached to EOVLAN
- Windows VirtIO adapter present
- matching NIC identity between hypervisor and guest
- static IPv4 configuration active
- `AddressState = Preferred`
- completed AD DS role installation
- successful domain/forest query
- PDC Emulator ownership on CORP-DC01
- configured DNS forwarder
- successful AD SRV lookup
- successful external DNS lookup through forwarding

Any screenshots used in the public repository should redact exact IP addresses, MAC addresses, usernames, credentials, or other environment-specific identifiers when they are not necessary to demonstrate the technical result.

## Next

- Build CORP-DC02 with Windows Server 2022 and the approved EOVLAN attachment.
- Configure CORP-DC02 with a reserved static address and CORP-DC01 as its DNS server before promotion.
- Verify Active Directory SRV discovery from CORP-DC02.
- Promote CORP-DC02 as an additional Domain Controller and DNS replica.
- Validate directory and DNS replication between the two controllers.
