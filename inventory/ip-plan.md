# Logical Address Plan

This is the public, sanitized version of the Azure lab addressing plan. Exact live addresses are intentionally omitted from the public repository.

## Corporate Addressing Model

The active Azure design uses a corporate Virtual Network (VNet) with separate subnets for:

- core infrastructure
- administration/management
- clients
- general workloads

Infrastructure services receive reserved private addresses through Azure network interface configuration. Guest operating systems remain DHCP-managed so they retain Azure platform gateway behavior.

## Static Infrastructure Roles

| Host / Service | Addressing Model | Purpose | Status |
| --- | --- | --- | --- |
| CORP-DC01 | Azure NIC reserved private IPv4 | First Domain Controller / DNS | Planned Azure rebuild |
| CORP-DC02 | Azure NIC reserved private IPv4 | Replica Domain Controller / DNS / Global Catalog | Planned Azure rebuild |
| CORP-NTP01 | Azure NIC reserved private IPv4 | Internal RHEL 9 chrony/NTP server | Planned |
| CORP-NTP02 | Azure NIC reserved private IPv4 | Redundant RHEL 9 chrony/NTP server | Planned |
| CORP-FS01 | Azure NIC reserved private IPv4 | File services | Planned |
| CORP-CA01 | Azure NIC reserved private IPv4 | Certificate services / PKI | Planned |
| CORP-OPS01 | Azure NIC reserved private IPv4 | Operations services | Planned |
| CORP-ADM01 | Azure NIC reserved private IPv4 | Administrative host | Planned |
| ROCKY-APP01 | Azure NIC reserved private IPv4 | Rocky Linux application host | Planned |
| PARTNER-DC01 | Azure NIC reserved private IPv4 | Partner forest directory services | Planned |
| PARTNER-CL01 | Azure private address | Partner client/member | Planned |

## DNS Plan

The Azure build preserves the validated AD DNS design while changing the network implementation.

Planned state:

- CORP-DC01 and CORP-DC02 host Active Directory-integrated DNS.
- The corporate VNet uses the Domain Controllers as custom DNS after both are healthy.
- Corporate members use only the internal AD DNS tier for private directory discovery.
- External recursion is handled through documented DNS forwarding.
- Partner DNS remains authoritative for its own forest and uses conditional forwarding across approved VNet peering for cross-forest discovery.
- Reverse zones correspond to the Azure subnet prefixes actually used.

## Time-Service Plan

- CORP-NTP01 and CORP-NTP02 run RHEL 9 and chrony.
- Each time server synchronizes independently to approved NIST public NTP sources.
- The forest-root PDC Emulator uses the two internal NTP servers.
- Other Windows Domain Controllers and members follow the normal Active Directory domain time hierarchy.
- The NTP tier remains standalone so directory-authentication trouble does not remove the authoritative internal time source.

## DHCP Principle

Azure Virtual Network supplies address configuration to Azure VM interfaces. Windows DHCP is therefore not the source of ordinary Azure VM NIC addressing.

A later optional Windows DHCP exercise will demonstrate role administration and failover using an appropriate relay/nested scenario rather than falsely representing Windows DHCP as the Azure VNet address authority.
