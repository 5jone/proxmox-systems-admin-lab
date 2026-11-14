# Server Inventory

This public inventory records the project systems and roles without publishing live Azure addresses, subscription identifiers, credentials, or other unnecessary environment details.

## Phase 0 — Proxmox Evidence

| Hostname | Operating System | Role | Platform | Status |
| --- | --- | --- | --- | --- |
| CORP-DC01 | Windows Server 2022 | First AD DS Domain Controller and DNS server | Proxmox VE | Completed and validated |
| CORP-DC02 | Windows Server 2022 | Replica AD DS Domain Controller, DNS, Global Catalog | Proxmox VE | Completed and validated |

The Phase 0 systems are retained as historical portfolio evidence. They are not the authoritative active lab platform after the Azure migration decision.

## Phase 1 — Azure Target Inventory

| Hostname | Operating System | Role | Azure placement | Status |
| --- | --- | --- | --- | --- |
| CORP-DC01 | Windows Server 2022 Datacenter: Azure Edition | AD DS / DNS / forest-root role owner | Corporate core subnet | Planned rebuild |
| CORP-DC02 | Windows Server 2022 Datacenter: Azure Edition | Replica AD DS / DNS / Global Catalog | Corporate core subnet | Planned rebuild |
| CORP-NTP01 | RHEL 9 PAYG | Internal NTP / chrony | Corporate core subnet | Planned |
| CORP-NTP02 | RHEL 9 PAYG | Redundant internal NTP / chrony | Corporate core subnet | Planned |
| CORP-FS01 | Windows Server 2022 | File services | Corporate core subnet | Planned |
| CORP-CA01 | Windows Server 2022 | AD CS / PKI | Corporate core subnet | Planned |
| CORP-OPS01 | Windows Server 2022 | Operations services | Corporate core subnet | Planned |
| CORP-ADM01 | Windows Server 2022 | Administrative management host | Management subnet | Planned |
| CORP-CL01 | Windows client when licensed, otherwise disposable member server | Domain/client policy testing | Client subnet | Planned |
| ROCKY-APP01 | Rocky Linux 9 | Linux application / SELinux / service administration | Workload subnet | Planned |
| PARTNER-DC01 | Windows Server 2022 | Partner forest Domain Controller / DNS | Separate partner VNet | Planned |
| PARTNER-CL01 | Windows client/member | Partner forest access testing | Separate partner VNet | Planned |

## Azure Baseline Principles

- No ordinary workload VM receives a public IP address.
- Infrastructure private addresses are reserved at the Azure NIC layer.
- Guest network interfaces remain DHCP-managed so Azure can supply the platform gateway and network configuration.
- Domain members use the AD DNS tier.
- RHEL NTP servers remain independent of AD DNS where practical and use the Azure-provided resolver path for public upstream lookup.
- Trusted Launch, Secure Boot, and virtual TPM are used where supported.
- Auto-shutdown and explicit deallocation are part of normal lab operations.
- Backups, monitoring, and patch management are Azure-native services once those chapters are implemented.
