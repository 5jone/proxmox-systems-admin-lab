# Network Inventory

This public inventory documents the Azure network design while intentionally omitting live addresses and account-specific identifiers.

## Active Cloud Network

| Item | Design |
| --- | --- |
| Cloud platform | Microsoft Azure |
| Primary region | East US 2, subject to quota/SKU availability |
| Corporate VNet | Dedicated private corporate VNet |
| Corporate segmentation | Core, management, client, and workload subnets |
| Workload public IPs | None by default |
| Administration | Azure Bastion Developer where supported |
| Network policy | Subnet/NIC NSGs plus guest firewalls |
| Corporate DNS | CORP-DC01 / CORP-DC02 after AD DNS is healthy |
| Internal time | CORP-NTP01 / CORP-NTP02 on RHEL 9 / chrony |
| Backup | Azure Backup |
| Monitoring | Azure Monitor + Log Analytics |
| Patching | Azure Update Manager |
| Planned partner boundary | Separate non-overlapping partner VNet + peering |
| Optional branch boundary | Temporary second-region VNet for real AD site practice |

## Network Design Principles

### Segmentation

The corporate address space is divided into dedicated subnets rather than recreating the original single Layer-2 Proxmox network. This creates real cloud routing and policy boundaries while keeping all corporate systems inside one logical private environment.

### Private Administration

Ordinary servers do not receive public IP addresses. Administrative access is provided through Azure Bastion Developer where supported or another documented Azure-native management/recovery path.

### Azure Address Assignment

Infrastructure addresses are reserved at the Azure NIC layer. The guest operating system remains DHCP-managed so Azure supplies the platform gateway and interface configuration.

### DNS

After the Azure Domain Controllers are healthy, the corporate VNet uses the internal AD DNS servers as custom DNS. Cross-forest name resolution is handled with conditional forwarders across explicitly permitted VNet peering.

### Time

The authoritative internal time tier is two standalone RHEL 9 servers running chrony. Those servers synchronize to approved NIST upstream sources. The forest-root PDC Emulator uses the internal pair, and other Windows systems use the AD domain hierarchy.

### Outbound Connectivity

Outbound access is an explicit design decision. The lab will first document the actual Azure default behavior and later implement an Azure NAT Gateway exercise for deterministic production-style egress when the cost/learning value justifies it.

### Partner Network

The partner forest is placed in a separate non-overlapping VNet. VNet peering and NSGs permit only the DNS, trust, authentication, and resource flows required by the exercise.

### Optional Second Region

A temporary second-region VNet can be created later to demonstrate a genuine Active Directory Branch site, global VNet peering, and intersite replication behavior.

## Phase 0 Network Evidence

The original Proxmox VLAN/VNet configuration is preserved only in Phase 0 build logs as historical evidence. It is not the active network architecture for the Azure phase.
