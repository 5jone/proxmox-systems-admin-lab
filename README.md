# Proxmox Systems Administration Lab

Hands-on systems administration portfolio lab built on Proxmox VE to practice Windows Server, Linux, networking, identity, infrastructure services, automation, security hardening, validation, and operational documentation.

This public repository is intentionally sanitized. Exact host IP addresses, gateway addresses, DNS resolver addresses, MAC addresses, and other live environment identifiers are omitted while preserving the technical design and validation process.

## Current Architecture

- Hypervisor: Proxmox VE
- Primary lab attachment: administrator-managed `EOVLAN` VNet
- VLAN: 152
- Endpoint network: assigned private /24 subnet
- Guest NIC VLAN tag: blank by design because VLAN membership is provided by the VNet
- Current server: `CORP-DC01`
- Operating system: Windows Server 2022 Standard Desktop Experience
- Planned role: first Active Directory Domain Services (AD DS) Domain Controller and internal DNS server

The existing environment provides bootstrap gateway, DNS, and DHCP services. Those services are being used only to establish and validate initial connectivity. The completed lab will transition to internal Active Directory-integrated DNS and dedicated Windows DHCP services.

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
| Active Directory Domain Services and DNS promotion | Next |

## Validation Approach

Configuration is not treated as complete until it is verified.

For the current CORP-DC01 networking milestone, validation included:

- Confirming the Windows VirtIO network adapter was detected correctly.
- Matching the virtual NIC identity between Proxmox and Windows.
- Observing the initial DHCP-provided network configuration before moving to static addressing.
- Disabling DHCP and confirming the previous lease was removed.
- Applying the reserved static configuration.
- Confirming the Windows address state became `Preferred`.
- Validating DNS resolution through the bootstrap resolver.
- Validating outbound HTTPS connectivity using a TCP port 443 test.

See [CORP-DC01 build log](build-logs/CORP-DC01.md) for the detailed implementation record.

## Repository Structure

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
