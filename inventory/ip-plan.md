# Logical IP Address Plan

This is the public, sanitized version of the lab addressing plan.

Exact IP addresses are intentionally omitted. The purpose of this file is to document address ownership, role separation, and allocation strategy without publishing the live addressing details of the environment.

## Static Infrastructure Roles

| Host / Service | Addressing Model | Purpose | Status |
| --- | --- | --- | --- |
| Existing infrastructure | Pre-existing | Gateway, bootstrap DHCP, and approved upstream DNS forwarding target | Existing |
| CORP-DC01 | Reserved static IPv4 | First Domain Controller and authoritative DNS server | Active / promoted |
| CORP-DC02 | Reserved static IPv4 | Second Domain Controller and DNS replica | Planned |
| PARTNER-DC01 | Reserved static IPv4 | Partner directory services | Planned |
| BRANCH-DC01 | Reserved static IPv4 | Additional Domain Controller / replica role | Planned |
| CORP-FS01 | Reserved static IPv4 | File services | Planned |
| CORP-CA01 | Reserved static IPv4 | Certificate services / Public Key Infrastructure | Planned |
| CORP-OPS01 | Reserved static IPv4 | Operations and management services | Planned |
| CORP-WSUS01 | Reserved static IPv4 | Windows Server Update Services | Planned |
| CORP-DHCP01 | Reserved static IPv4 | Primary Windows DHCP server | Planned |
| CORP-DHCP02 | Reserved static IPv4 | DHCP failover peer | Planned |
| ROCKY-APP01 | Reserved static IPv4 | Rocky Linux application host | Planned |
| ROCKY-AUTO01 | Reserved static IPv4 | Rocky Linux automation host | Planned |
| CORP-ADM01 | Reserved static IPv4 | Administrative workstation | Planned |
| PARTNER-CL01 | Reserved/static as required | Partner client | Planned |
| BRANCH-CL01 | Reserved/static as required | Branch client | Planned |
| Corporate client pool | DHCP scope | Future client lease range | Planned / inactive |

## DNS Plan

CORP-DC01 has completed its transition from bootstrap DNS to internal Active Directory DNS.

Current state:

- CORP-DC01 uses its own local DNS service as its client resolver.
- CORP-DC01 is authoritative for `corp.example.test`.
- Active Directory SRV records are registered and resolve successfully.
- External names are forwarded to the approved existing infrastructure resolver.
- No public resolver is configured directly on the Domain Controller's network adapter.

Planned next state:

- CORP-DC02 will use CORP-DC01 for DNS while joining and promoting.
- CORP-DC02 will also host DNS after promotion.
- Once both Domain Controllers are healthy, corporate member systems will use the two internal Domain Controllers as their DNS servers.

## DHCP Plan

The existing DHCP service observed during bootstrap is not the final lab DHCP design.

The finished environment will use two dedicated Windows DHCP servers with failover.

The future corporate client scope will remain inactive until:

- the lab DHCP authority is approved,
- address exclusions and reservations are confirmed,
- conflict checks are complete, and
- the existing infrastructure DHCP boundary is understood.

## Address Management Principle

Infrastructure systems use reserved static addressing because services such as Active Directory, DNS, DHCP, certificate services, file services, and management tooling require predictable endpoints.

Client systems can use DHCP once the authorized scope and failover configuration are in place.
