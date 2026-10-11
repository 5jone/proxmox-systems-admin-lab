# Network Inventory

This public inventory documents the network design while intentionally omitting exact live IP addresses and other unnecessary environment identifiers.

## Primary Lab Network

| Item | Value |
| --- | --- |
| Proxmox VNet | `EOVLAN` |
| VLAN ID | `152` |
| Endpoint network | Assigned private /24 subnet |
| Default gateway | Existing infrastructure gateway |
| Bootstrap DNS | Existing infrastructure resolver |
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

During CORP-DC01 deployment, the existing environment supplied a temporary DHCP lease along with gateway and DNS information.

That service was useful for initial connectivity validation but is not the final lab design.

CORP-DC01 was subsequently moved to its reserved static configuration.

The completed lab will use:

- Active Directory-integrated DNS on the Domain Controllers.
- Dedicated Windows DHCP servers with failover.

Until those services are deployed and authorized, the existing infrastructure services should be understood as bootstrap dependencies rather than lab-managed infrastructure.

## Current Validation

CORP-DC01 has demonstrated:

- Layer 2 attachment through EOVLAN.
- Correct guest-side VLAN handling.
- Valid static addressing on the assigned private subnet.
- Reachability through the existing gateway.
- DNS resolution through the bootstrap resolver.
- Outbound TCP port 443 connectivity.
