# Azure Cloud Engineering + Systems Administration Lab

Hands-on cloud engineering and systems administration portfolio project built around Microsoft Azure, Windows Server, Red Hat Enterprise Linux (RHEL), Rocky Linux, identity, networking, security, automation, monitoring, backup, and recovery.

**Interview-facing portfolio site:** https://5jone.github.io/proxmox-systems-admin-lab/

> The repository name is retained for continuity with Phase 0. The authoritative active build has moved to Microsoft Azure.

This public repository is intentionally sanitized. Exact subscription identifiers, tenant identifiers, public/private IP addresses, credentials, MAC addresses, recovery secrets, and other live environment identifiers are omitted while preserving the technical design and validation process.

## Project Evolution

### Phase 0 — Proxmox systems administration foundation — Complete

The first phase established and validated a traditional virtualization baseline:

- Windows Server 2022 virtual machines on Proxmox VE
- `CORP-DC01` and `CORP-DC02`
- Active Directory Domain Services (AD DS)
- Active Directory-integrated Domain Name System (DNS)
- Global Catalog redundancy
- SYSVOL / NETLOGON validation
- Active Directory replication validation
- Windows domain time hierarchy
- troubleshooting and recovery evidence

The Phase 0 build logs remain part of the portfolio because they demonstrate hypervisor administration, Windows deployment, Active Directory troubleshooting, and evidence-driven validation.

### Phase 1 — Azure cloud engineering build — Active

The environment is now being rebuilt cleanly in Microsoft Azure rather than copying the Proxmox VM disks. The rebuild is deliberate: it preserves the systems-administration learning while adding cloud networking, governance, secure management, monitoring, backup, automation, and cost controls.

## Authoritative Azure Architecture

- Cloud platform: Microsoft Azure
- Primary region: East US 2, subject to SKU/quota availability
- Resource organization: dedicated lab resource group
- Corporate VNet: segmented private address space with dedicated core, management, client, and workload subnets
- Workload exposure: no public IP addresses on ordinary servers
- Administration: Azure Bastion Developer where supported, plus Azure-native recovery/management paths
- Network policy: Network Security Groups (NSGs) plus guest firewalls
- Windows identity tier: redundant Windows Server 2022 Domain Controllers
- Linux time tier: redundant RHEL 9 servers running chrony
- Linux application/admin practice: Rocky Linux 9
- Backup: Azure Backup
- Monitoring: Azure Monitor + Log Analytics + Azure Monitor Agent
- Patching: Azure Update Manager
- Security baseline: Trusted Launch / Secure Boot / vTPM where supported
- Cost governance: budget alerts, auto-shutdown, and deliberate VM deallocation
- Infrastructure as Code progression: Azure Portal → Azure CLI → Bicep → Terraform

## Current Milestones

| Milestone | Status |
| --- | --- |
| Phase 0 CORP-DC01 build and validation | Complete |
| Phase 0 CORP-DC02 promotion and replication validation | Complete |
| Phase 0 DNS, SYSVOL/NETLOGON, and domain-time validation | Complete |
| Azure migration architecture and build guide | Complete |
| Azure resource group + cost guardrails | Next |
| Corporate Azure VNet / subnet segmentation | Planned |
| Secure Azure management path | Planned |
| Azure CORP-DC01 / CORP-DC02 rebuild | Planned |
| RHEL 9 CORP-NTP01 / CORP-NTP02 time tier | Planned |
| Azure Monitor / Backup / Update Manager | Planned |
| Azure partner VNet / forest trust | Planned |
| Bicep / Terraform implementation | Planned |

## Why the Project Moved

The original shared virtualization environment was valuable for learning the fundamentals, but it also introduced constraints that were unrelated to the target career path. Rebuilding the same enterprise services in Azure creates a stronger cloud-engineering project:

- real cloud virtual networking and segmentation
- Azure-native security boundaries
- managed disks and cloud VM lifecycle
- secure private administration
- explicit outbound/egress design
- Azure monitoring and backup
- cost management
- Role-Based Access Control (RBAC) and policy
- Infrastructure as Code
- multi-VNet and optional multi-region design

## Validation Approach

Configuration is not treated as complete until behavior is proven. Evidence includes:

- platform configuration
- operating-system state
- DNS and service discovery
- replication health
- allowed and denied network transactions
- backup and restore tests
- patching results
- monitoring/alert evidence
- failure injection and rollback
- cost and lifecycle controls
- repeatable automation

Phase 0 remains documented as completed evidence. Phase 1 evidence is added only as Azure resources are actually built and validated.

## Repository Structure

- `docs/` — GitHub Pages interview-facing presentation site
- `build-logs/` — chronological implementation and troubleshooting records
- `inventory/` — sanitized server, network, and logical addressing documentation
- `screenshots/` — selected implementation and validation evidence
- `ansible/` — Linux configuration automation
- `terraform/` — Azure Infrastructure as Code as the project reaches that phase
- `diagrams/` — current architecture and topology diagrams

## Planned Technical Scope

The active Azure lab is designed to demonstrate:

- Microsoft Azure infrastructure administration
- Active Directory Domain Services (AD DS)
- Domain Name System (DNS)
- Windows Server administration
- RHEL 9 and Rocky Linux 9 administration
- Network Time Protocol (NTP) / chrony
- Azure Virtual Networks and subnet segmentation
- Network Security Groups (NSGs)
- Azure Bastion
- Azure Backup
- Azure Monitor and Log Analytics
- Azure Update Manager
- Role-Based Access Control (RBAC)
- Azure Policy
- Group Policy
- file services and permissions
- Public Key Infrastructure (PKI)
- Security Technical Implementation Guide (STIG) hardening
- vulnerability remediation
- PowerShell, Bash, Ansible, Azure CLI, Bicep, and Terraform
- optional VNet peering, partner forest, and second-region AD site exercises
