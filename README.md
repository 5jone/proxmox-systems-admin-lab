# Proxmox Systems Administration Lab

Hands-on systems administration portfolio lab built on Proxmox VE to practice Windows Server, Linux, networking, identity, infrastructure services, automation, security hardening, validation, and operational documentation.

**Interview-facing portfolio site:** https://5jone.github.io/proxmox-systems-admin-lab/

This public repository is intentionally sanitized. Exact host IP addresses, gateway addresses, DNS resolver addresses, MAC addresses, and other live environment identifiers are omitted while preserving the technical design and validation process.

## Current Architecture

- Hypervisor: Proxmox VE
- Primary lab attachment: administrator-managed `EOVLAN` VNet
- VLAN: 152
- Endpoint network: assigned private /24 subnet
- Guest NIC VLAN tag: blank by design because VLAN membership is provided by the VNet
- Current identity tier: `CORP-DC01` and `CORP-DC02`
- Operating system: Windows Server 2022 Standard Desktop Experience
- Current role: redundant Active Directory Domain Services (AD DS) Domain Controllers and Active Directory-integrated DNS servers
- Forest / domain: `corp.example.test`
- NetBIOS name: `CORP`
- Current time hierarchy: CORP-DC02 follows the Active Directory domain hierarchy through the forest-root PDC Emulator
- Next infrastructure milestone: redundant Rocky Linux 9 NTP servers using chrony, feeding the forest-root PDC Emulator

The existing environment still provides the off-subnet gateway and the approved upstream DNS resolver. CORP-DC01 now uses its own internal DNS service for Active Directory resolution and forwards unresolved external queries to the approved upstream resolver.

## Current Milestones

| Milestone | Status |
| --- | --- |
| CORP-DC01 virtual machine created | Complete |
| Windows Server 2022 installed | Complete |
| VirtIO drivers installed | Complete |
| QEMU Guest Agent installed and validated | Complete |
| CORP-DC01 attached to EOVLAN | Complete |
| Proxmox and Windows NIC identity matched | Complete |
| Static IPv4 configuration applied | Complete |
| Default gateway path validated | Complete |
| Bootstrap DNS resolution validated | Complete |
| Outbound TCP/443 connectivity validated | Complete |
| Windows patching completed before promotion | Complete |
| CORP-DC01 memory increased to 8 GiB fixed | Complete |
| AD DS role installed | Complete |
| New forest and root domain created | Complete |
| DNS installed on CORP-DC01 | Complete |
| AD SRV discovery validated | Complete |
| External DNS forwarding validated | Complete |
| CORP-DC02 virtual machine provisioned | Complete |
| Windows Server 2022 installation on CORP-DC02 | Complete |
| CORP-DC02 replica promotion and replication validation | Complete |
| CORP-DC02 DNS, SYSVOL, NETLOGON, and domain-time validation | Complete |
| CORP-NTP01 / CORP-NTP02 Rocky Linux time tier | Next |

## Validation Approach

Configuration is not treated as complete until it is verified.

For CORP-DC01, validation has included:

- Confirming the Windows VirtIO network adapter was detected correctly.
- Matching the virtual NIC identity between Proxmox and Windows.
- Observing the initial DHCP-provided network configuration before moving to static addressing.
- Disabling DHCP and confirming the previous lease was removed.
- Applying the reserved static configuration.
- Confirming the Windows address state became `Preferred`.
- Validating gateway reachability and outbound HTTPS connectivity.
- Completing Windows servicing before directory promotion.
- Confirming the promoted domain reports the expected DNS root, functional level, and PDC Emulator.
- Verifying the LDAP Domain Controller discovery SRV record resolves to CORP-DC01.
- Verifying external DNS resolution through the configured forwarder.
- Promoting CORP-DC02 as an additional Domain Controller and DNS server.
- Verifying both controllers are Global Catalog servers.
- Verifying Active Directory replication reports zero failures.
- Verifying SYSVOL and NETLOGON are published on CORP-DC02.
- Querying CORP-DC02 directly for LDAP Domain Controller SRV records and confirming both controllers are returned.
- Configuring reciprocal Domain Controller DNS-client order for resolver redundancy.
- Returning CORP-DC02 to the Active Directory domain time hierarchy and confirming CORP-DC01 is its active source.

See [CORP-DC01 build log](build-logs/CORP-DC01.md) and [CORP-DC02 build log](build-logs/CORP-DC02.md) for the detailed implementation records.

## Repository Structure

- `docs/` — GitHub Pages interview-facing presentation site
- `build-logs/` — chronological implementation records
- `inventory/` — sanitized server, network, and logical addressing documentation
- `screenshots/` — implementation and validation evidence
- `ansible/` — future Linux configuration automation
- `terraform/` — future Proxmox infrastructure automation
- `diagrams/` — architecture and topology diagrams

## Planned Technical Scope

The lab is being expanded to demonstrate:

- Active Directory Domain Services (AD DS)
- Domain Name System (DNS)
- Dynamic Host Configuration Protocol (DHCP) and failover
- Group Policy
- file services and permissions
- Public Key Infrastructure (PKI)
- Linux administration and directory integration
- patching and monitoring
- backup and recovery
- Security Technical Implementation Guide (STIG) hardening
- SCAP Compliance Checker (SCC) validation
- vulnerability remediation
- PowerShell, Bash, Ansible, and Terraform automation
