# CORP-DC02

## Status

CORP-DC02 is online as the second Windows Server 2022 Active Directory Domain Services (AD DS) Domain Controller, Active Directory-integrated Domain Name System (DNS) server, and Global Catalog server for the `corp.example.test` forest.

Promotion, directory replication, DNS replication, SYSVOL/NETLOGON publication, reciprocal DNS-client configuration, and Active Directory domain-hierarchy time synchronization have been validated.

> Public portfolio note: exact IP addresses, MAC addresses, VM identifiers, gateway/resolver addresses, credentials, and other live environment identifiers are intentionally omitted.

## Platform

- Hypervisor: Proxmox VE
- Operating System: Windows Server 2022 Standard Desktop Experience
- Current role: Replica AD DS Domain Controller
- DNS role: Active Directory-integrated DNS
- Global Catalog: Enabled
- Forest / domain: `corp.example.test`
- Network: EOVLAN
- VLAN: 152
- Addressing: Reserved static IPv4
- DNS-client design: Prefer CORP-DC01, use CORP-DC02 itself as alternate
- Time design: Active Directory domain hierarchy; current source validated as CORP-DC01

## Virtual Hardware

- Machine: q35
- Firmware: OVMF / UEFI
- EFI disk: enabled
- CPU: 2 virtual cores
- CPU type: host
- Memory: 8 GiB fixed
- Memory ballooning: disabled
- Disk controller: VirtIO SCSI Single
- System disk: 64 GiB qcow2
- I/O thread: enabled
- Network adapter: VirtIO
- Network attachment: EOVLAN
- Guest VLAN tag: blank by design
- QEMU Guest Agent integration: enabled
- Proxmox HA: not enabled

## Build Log

### 2026-11-11 — Virtual machine provisioning

- Created the CORP-DC02 virtual machine using the administrator-approved VLAN-based VM numbering convention.
- Selected q35 and OVMF / UEFI with an EFI disk.
- Selected VirtIO SCSI Single and provisioned a 64 GiB qcow2 system disk.
- Configured two virtual CPU cores using the host CPU type.
- Provisioned 8 GiB fixed memory with ballooning disabled.
- Attached a VirtIO NIC to EOVLAN with the guest VLAN tag blank because VLAN membership is supplied by the VNet.
- Enabled QEMU Guest Agent integration on the Proxmox side.
- Left Proxmox High Availability (HA) disabled because directory redundancy and hypervisor HA are separate concerns.

### 2026-11-13 to 2026-11-14 — Windows deployment, troubleshooting, and promotion

- Installed Windows Server 2022 Standard Desktop Experience.
- Installed the required VirtIO guest drivers and confirmed the network adapter was operational.
- Renamed the server to CORP-DC02.
- Applied the reserved static IPv4 configuration and used CORP-DC01 for pre-promotion Active Directory DNS discovery.
- Verified Active Directory DNS service-location records and connectivity to the existing Domain Controller.
- Installed the AD DS role and management tools.
- During promotion troubleshooting, identified and corrected a real multi-hour clock skew between the two servers before continuing.
- Corrected CORP-DC01 DNS-client configuration so Windows could identify the domain network correctly instead of classifying the connection as Public.
- Validated CORP-DC01 domain discovery and LDAP binding after the DNS correction.
- Determined that explicitly forcing `-ReplicationSourceDC` was unnecessary in this one-source bootstrap environment and caused prerequisite validation to fail.
- Ran `Test-ADDSDomainControllerInstallation` without the optional forced replication-source parameter; all prerequisite tests completed successfully.
- Promoted CORP-DC02 using normal automatic replication-source selection.
- Allowed the promotion-triggered reboot to complete and signed in with the domain administrator identity.

### 2026-11-14 — Post-promotion validation

- Confirmed Active Directory reports both CORP-DC01 and CORP-DC02 as Domain Controllers and Global Catalog servers.
- Ran `repadmin /replsummary` and confirmed zero replication failures for both source and destination summaries.
- Confirmed CORP-DC02 publishes both SYSVOL and NETLOGON shares.
- Queried CORP-DC02's DNS service directly for the LDAP Domain Controller discovery SRV record and confirmed records for both Domain Controllers.
- Configured reciprocal Domain Controller DNS-client order so each controller prefers the other and retains itself as alternate.
- Returned CORP-DC02 to the Active Directory domain time hierarchy with `w32tm /config /syncfromflags:domhier /update`.
- Forced time rediscovery/resynchronization and confirmed CORP-DC02 selected CORP-DC01 as its source.
- Verified Windows Time reports no leap warning and a successful recent synchronization.

## Validation

- [x] Windows Server 2022 installed.
- [x] VirtIO guest drivers installed.
- [x] QEMU Guest Agent service installed and available.
- [x] Hostname is CORP-DC02.
- [x] Reserved static IPv4 configuration applied.
- [x] CORP-DC01 used for pre-promotion DNS discovery.
- [x] Active Directory SRV discovery validated.
- [x] AD DS role installed.
- [x] Promotion prerequisites passed using automatic replication-source selection.
- [x] CORP-DC02 promoted as an additional Domain Controller.
- [x] CORP-DC01 and CORP-DC02 both report Global Catalog capability.
- [x] Active Directory replication summary reports zero failures.
- [x] SYSVOL and NETLOGON published on CORP-DC02.
- [x] CORP-DC02 DNS directly returns LDAP SRV records for both Domain Controllers.
- [x] Reciprocal DNS-client resolver order configured on the two Domain Controllers.
- [x] CORP-DC02 synchronized through the Active Directory domain time hierarchy.

## Troubleshooting Lessons

### Optional parameters should have an operational reason

The first promotion attempts explicitly set `-ReplicationSourceDC` to CORP-DC01. In this bootstrap environment there was only one existing Domain Controller, so automatic source selection was already deterministic. Removing the unnecessary constraint allowed the prerequisite test and promotion to proceed normally.

The lesson is not to avoid the parameter universally. In a larger environment it can be useful for controlled source selection. The lesson is to use optional controls when they solve a real operational requirement rather than adding them automatically.

### Time is an authentication dependency

A multi-hour skew was discovered while troubleshooting promotion. Active Directory and Kerberos depend on consistent time, so clock validation belongs early in directory troubleshooting even when basic network ports are reachable.

### DNS controls domain discovery

CORP-DC01 was temporarily classified under the Public Windows network profile until its DNS-client path was corrected and fresh domain discovery succeeded. The exercise reinforced that successful TCP connectivity alone does not prove healthy Active Directory discovery.

## Why 8 GiB is the baseline

CORP-DC01 demonstrated that 4 GiB was functional but unnecessarily tight during cumulative Windows servicing. CORP-DC02 therefore uses 8 GiB fixed memory to provide predictable headroom for Windows Server, patching, AD DS, DNS, and management tools.

## Why Proxmox HA is not enabled

A second Domain Controller improves Active Directory and DNS service redundancy, but both Domain Controllers still share the same Proxmox host failure domain.

Proxmox HA is a separate hypervisor-cluster capability intended to restart workloads on another node after a host failure. That is not part of the current single-node lab design.

## Next

- Build CORP-NTP01 and CORP-NTP02 as Rocky Linux 9 chrony servers.
- Validate each internal NTP server independently against the approved NIST upstream service.
- Configure the forest-root PDC Emulator to use the internal NTP pair.
- Keep CORP-DC02 and ordinary domain members on the Active Directory domain time hierarchy.
- Perform a bounded one-NTP-server failure test and verify time-service resilience.
- Continue with CORP-ADM01 and CORP-FS01 after the time tier is validated.
