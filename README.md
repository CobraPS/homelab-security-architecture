# Homelab Security Architecture

A personal homelab used to develop hands-on experience with network security, Linux administration, virtualization, firewall policy, storage, self-hosted services, and defensive security monitoring.

The environment is built around **OPNsense**, **Proxmox VE**, **TrueNAS**, and separate networks for personal devices, servers, and wired IP cameras.

## Architecture

![Homelab network architecture](diagrams/network-architecture.svg)

The editable Mermaid source for the diagram is available at [`diagrams/network-architecture.mmd`](diagrams/network-architecture.mmd).

## Physical Topology

Internet connectivity enters through a cable modem connected to the WAN interface of a **Sophos XG 125 Rev.3 running OPNsense**.

OPNsense connects to a **D-Link DGS-1210-24P managed switch**, which provides the central switching and VLAN infrastructure for the network.

The managed switch connects to three downstream areas:

- A **2.5 GbE unmanaged switch** for server and storage infrastructure:
  - ZimaBoard running Proxmox VE
  - HP EliteDesk Mini PC running Proxmox VE
  - ZimaBlade running TrueNAS

- A **1 GbE unmanaged switch** for the personal network:
  - Desktop workstation running Frigate
  - Netgear router configured in access point mode

- A separate **1 GbE unmanaged switch** for:
  - Wired IP cameras

The Netgear router operates only as a wireless access point on the personal network. Routing, firewall enforcement, and inter-network traffic control are handled by OPNsense.

## Network Segmentation

The network is separated into security zones based on device purpose and trust level.

| Network | Subnet | Purpose |
|---|---|---|
| Personal | `192.168.1.0/24` | Personal computers, mobile devices, wireless clients, and other trusted devices |
| Cameras | `10.0.11.0/24` | Wired IP surveillance cameras |
| Servers | `10.0.30.0/24` | Proxmox hosts, TrueNAS, VMs, containers, and self-hosted services |

### Personal Network

The personal network contains trusted user devices, including my desktop workstation and wireless clients.

The desktop also runs **Frigate**, which acts as the NVR backend for the camera system.

Devices on the personal network are permitted to access services hosted on the server VLAN.

### Server VLAN

All server infrastructure is placed on the `10.0.30.0/24` server VLAN.

This includes:

- ZimaBoard Proxmox host
- HP EliteDesk Proxmox host
- ZimaBlade TrueNAS server
- Virtual machines
- Linux containers
- Docker workloads
- Self-hosted infrastructure services

Separating servers from normal client devices provides a distinct security boundary while still allowing trusted devices on the personal network to access required services.

### Camera VLAN

All surveillance cameras are wired and placed on the dedicated `10.0.11.0/24` camera VLAN.

The camera network is segmented from the personal and server networks, with communication controlled through OPNsense firewall policy.

The desktop running **Frigate** is permitted to access the cameras so it can retrieve and process video streams.

Camera Internet access is restricted by default. Explicit exceptions are used only for cameras that require external connectivity.

No cameras connect over Wi-Fi.

## Firewall and Routing

**OPNsense** serves as the primary router and firewall for the homelab.

It is responsible for:

- Inter-VLAN routing
- Firewall policy enforcement
- Network segmentation
- Controlling communication between network zones
- Restricting camera Internet access
- Allowing trusted personal devices to access server resources
- Allowing the Frigate host to access the camera network
- Providing firewall logs for troubleshooting and analysis

The design avoids placing all devices on a single trusted LAN and instead applies access based on device function and trust level.

## Virtualization and Compute

The homelab uses two Proxmox VE hosts.

### ZimaBoard

The ZimaBoard runs **Proxmox VE** and hosts Linux virtual machines and containers used for self-hosted services and infrastructure experimentation.

### HP EliteDesk Mini PC

The HP EliteDesk Mini PC also runs **Proxmox VE**, providing additional compute capacity for virtual machines, containers, and other workloads.

The Proxmox environment provides hands-on experience with:

- Linux administration
- Virtual machine management
- Linux containers
- Service deployment
- Network configuration
- Infrastructure troubleshooting
- Self-hosting

## Storage and Backups

A **ZimaBlade running TrueNAS** provides network storage.

The storage environment includes:

- Mirrored `2 x 12 TB` storage
- SMB network shares
- Local NAS backups of desktop, laptop, and mobile data
- Additional offsite cloud copies of important data

TrueNAS resides on the server VLAN alongside the rest of the server infrastructure.

## Self-Hosted Services

The homelab runs several services used both for practical purposes and infrastructure learning.

### Technitium DNS

Technitium provides DNS services within the homelab and gives additional control and visibility over name resolution.

### Tailscale

Tailscale provides authenticated remote access to selected homelab resources without requiring those services to be directly exposed to the public Internet.

### Home Assistant

Home Assistant provides home automation services within the homelab environment.

### Frigate

Frigate runs on the desktop workstation on the personal network and provides the backend NVR for the wired IP camera system.

OPNsense firewall policy permits the Frigate host to access cameras on the camera VLAN while the cameras remain segmented from the trusted personal network.

### Docker

Docker is used to run containerized applications and services within the server environment.

## Security Monitoring and Analysis

The homelab also provides an environment for defensive security practice and network troubleshooting.

Tools and data sources include:

- Wireshark packet captures
- OPNsense firewall logs
- Linux system logs
- DNS traffic
- Network connection behavior
- Inter-VLAN traffic
- Firewall allow and deny events

Wireshark and network logs are used to inspect traffic, troubleshoot connectivity problems, verify firewall behavior, and investigate unexpected network activity.

The segmented environment also provides a practical platform for testing how firewall rules affect communication between trusted clients, servers, and less-trusted IoT devices.

## Hardware

| Component | Role |
|---|---|
| Sophos XG 125 Rev.3 | OPNsense router and firewall |
| D-Link DGS-1210-24P | Managed switching and VLAN infrastructure |
| Unmanaged 2.5 GbE switch | Server and storage connectivity |
| ZimaBoard | Proxmox VE virtualization host |
| HP EliteDesk Mini PC | Proxmox VE virtualization host |
| ZimaBlade | TrueNAS storage server |
| Unmanaged 1 GbE switch | Desktop and wireless AP connectivity |
| Netgear router in AP mode | Wireless access for the personal network |
| Separate unmanaged 1 GbE switch | Wired camera connectivity |
| Wired IP cameras | Surveillance endpoints |
| Desktop workstation | Trusted client and Frigate NVR host |

## Technologies

### Networking and Security

- OPNsense
- VLANs
- Network segmentation
- Firewall policy
- Inter-VLAN routing
- Wireshark
- Packet analysis
- Log analysis
- DNS
- Tailscale

### Virtualization and Infrastructure

- Proxmox VE
- TrueNAS
- Docker
- Linux virtual machines
- Linux containers
- SMB
- Storage and backup administration

### Services

- Technitium DNS
- Home Assistant
- Frigate
- Tailscale

### Operating Systems

- Linux
- Ubuntu

## Skills Demonstrated

This homelab provides practical experience with:

- Network architecture and segmentation
- Firewall rule design
- Inter-VLAN routing
- Linux system administration
- Virtualization
- Containerized services
- Network-attached storage
- Backup design
- DNS administration
- Secure remote access
- Network troubleshooting
- Packet analysis
- Log analysis
- Defensive security monitoring
- Designing controlled communication between networks with different trust levels
