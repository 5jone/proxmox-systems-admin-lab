# Network Inventory

This public inventory documents the network design while intentionally omitting exact live IP addresses and other unnecessary environment identifiers.

## Primary Lab Network

| Item | Value |
| --- | --- |
| Proxmox VNet | `EOVLAN` |
| VLAN ID | `152` |
| Endpoint network | Assigned private /24 subnet |
| Default gateway | Existing infrastructure gateway |
| Domain Controller DNS clients | Reciprocal internal resolver order: each controller prefers the other and retains itself as alternate |
| External DNS path | Forwarded to approved infrastructure resolver |
| Windows time path | Non-PDC systems follow the Active Directory domain hierarchy |
| Planned internal NTP tier | CORP-NTP01 and CORP-NTP02 on Rocky Linux 9 / chrony |
| Guest NIC VLAN tag | Blank |
| Current attached servers | `CORP-DC01`; `CORP-DC02` promoted and validated |

## Design Notes

### EOVLAN and VLAN 152

The environment administrator confirmed that VLAN 152 is already mapped to the Proxmox `EOVLAN` VNet.

Lab VMs therefore attach directly to EOVLAN and leave the Proxmox guest NIC VLAN tag field blank.

The VLAN ID and the IPv4 subnet represent different layers of the design:

- VLAN 152 defines Layer 2 network membership.
- The assigned private /24 defines Layer 3 endpoint addressing.

### Gateway

The primary lab network uses an existing infrastructure gateway for off-subnet communication.

The Proxmox host is not the guest default gateway, and the optional lab firewall is not currently in the primary endpoint path.

### Bootstrap DHCP and DNS

During the initial CORP-DC01 deployment, the existing environment supplied a temporary DHCP lease along with gateway and DNS information.

That service was useful for initial connectivity validation but is not the final lab-managed identity design.

CORP-DC01 was subsequently moved to its reserved static configuration.

### Current Active Directory DNS design

CORP-DC01 is now authoritative for the `corp.example.test` Active Directory namespace and uses its own DNS service as its client resolver.

Internal Active Directory records are resolved locally. Queries for namespaces the server is not authoritative for are forwarded to the approved existing infrastructure resolver.

This separates two responsibilities:

- internal Active Directory name and service discovery, handled by CORP-DC01;
- external name resolution, handled through the configured forwarder.

CORP-DC02 is now promoted and healthy. Both Domain Controllers host Active Directory-integrated DNS, and each controller prefers the other for DNS resolution while retaining itself as alternate. Corporate systems use the internal Domain Controllers for Active Directory DNS rather than public resolvers.

### Current Active Directory time design

CORP-DC02 is configured to use the Active Directory domain time hierarchy and currently synchronizes through the forest-root PDC Emulator.

The next infrastructure milestone adds two standalone Rocky Linux 9 chrony servers. They will synchronize independently to the approved NIST public time service. The forest-root PDC Emulator will then use that internal pair, while other Windows systems remain on the domain hierarchy.

### Future DHCP design

The completed lab will use dedicated Windows DHCP servers with failover.

Until those services are deployed and authorized, the existing infrastructure DHCP service should be understood as a bootstrap dependency rather than lab-managed DHCP.

## Current Validation

The current two-controller environment has demonstrated:

- Layer 2 attachment through EOVLAN.
- Correct guest-side VLAN handling.
- Valid static addressing on the assigned private subnet.
- Reachability through the existing gateway.
- Successful AD DS forest creation.
- Two healthy Domain Controllers and Global Catalog servers.
- Zero failures in the Active Directory replication summary.
- SYSVOL and NETLOGON publication on CORP-DC02.
- Active Directory-integrated DNS on both Domain Controllers.
- LDAP Domain Controller SRV discovery from CORP-DC02 returning both replicas.
- Reciprocal Domain Controller DNS-client configuration.
- CORP-DC02 synchronization through the Active Directory domain time hierarchy.
- Successful external DNS forwarding.
- Outbound TCP port 443 connectivity.
