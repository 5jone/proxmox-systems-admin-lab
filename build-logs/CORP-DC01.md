\# CORP-DC01



\## Status

Installed, network pending



\## Platform

\- Hypervisor: Proxmox VE

\- VM ID: 1521

\- Operating System: Windows Server 2022 Standard Desktop Experience

\- Role: Planned Domain Controller

\- Network: Disconnected pending Chapter 3



\## Virtual Hardware

\- Machine: q35

\- Firmware: OVMF / UEFI

\- Disk Controller: VirtIO SCSI Single

\- System Disk: 64 GB

\- Disk Storage: EricOStore

\- Disk Format: qcow2

\- QEMU Guest Agent: Enabled



\## Build Log



\### 2026-10-04

\- Created VM 1521

\- Installed Windows Server 2022

\- Loaded VirtIO storage drivers

\- Installed NetKVM network driver

\- Installed QEMU Guest Agent

\- Renamed Windows host to CORP-DC01

\- Set system disk first in boot order

\- Ejected Windows Server installation ISO

\- Verified successful boot from scsi0

\- Network intentionally left disconnected



\## Validation

\- \[x] Boots from system disk

\- \[x] Computer name is CORP-DC01

\- \[x] Windows Server installation verified

\- \[x] Network remains disconnected

\- \[x] Verify Device Manager is clean

\- \[x] Verify QEMU Guest Agent service is running



\## Evidence

See `/screenshots/CORP-DC01/`



\## Next

\- Complete Chapter 3 isolated network deployment

\- Attach CORP-DC01 only to the corporate internal bridge

\- Patch Windows Server

\- Continue Chapter 4 IP addressing

\- Continue Chapter 5 Active Directory Domain Services promotion

