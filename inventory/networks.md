# Network Inventory

This public inventory documents the network design while intentionally omitting exact live IP addresses and other unnecessary environment identifiers.

## Primary Lab Network

| Item | Value |
| --- | --- |
| Proxmox VNet | `EOVLAN` |
| VLAN ID | `152` |
| Endpoint network | Assigned private /24 subnet |
| Default gateway | Existing infrastructure gateway |
| CORP-DC01 DNS client | Local authoritative DNS service |
| External DNS path | Forwarded to approved infrastructure resolver |
| Guest NIC VLAN tag | Blank |
| Current connected server | `CORP-DC01` |

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

The next replica Domain Controller will use CORP-DC01 for DNS during domain discovery and promotion. After both controllers are operational, corporate systems will use the internal Domain Controllers as their DNS servers.

### Future DHCP design

The completed lab will use dedicated Windows DHCP servers with failover.

Until those services are deployed and authorized, the existing infrastructure DHCP service should be understood as a bootstrap dependency rather than lab-managed DHCP.

## Current Validation

CORP-DC01 has demonstrated:

- Layer 2 attachment through EOVLAN.
- Correct guest-side VLAN handling.
- Valid static addressing on the assigned private subnet.
- Reachability through the existing gateway.
- Successful AD DS forest creation.
- Successful authoritative DNS operation for the lab domain.
- Successful LDAP Domain Controller SRV discovery.
- Successful external DNS forwarding.
- Outbound TCP port 443 connectivity.
