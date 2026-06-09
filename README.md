# proxmox-network-lab

A self built home networking lab on Proxmox VE, created to develop hands-on Linux and networking skills. The lab runs entirely on virtual machines and implements core networking concepts from the ground up: static routing, VLAN segmentation, router on a stick inter VLAN routing, and secure remote access. All configured manually.

## Overview

- **Hypervisor:** Proxmox VE 9.2 (Type 1, bare metal)
- **Host hardware:** Ryzen 7 3700X, 16 GB DDR4
- **Guests:** Ubuntu Server 26.04 VMs, configured via Netplan
- **Remote access:** Tailscale (WireGuard-based mesh VPN)

This lab was built incrementally, with each milestone tested and verified before moving to the next. The goal was not just to make it work, but to understand *why* each piece works and to debug the failures along the way.

## Network Topology

The lab implements two distinct routing scenarios on isolated virtual networks.

### Inter-VLAN routing (router on a stick)

VM1 acts as a router, carrying both VLANs over a single trunk link and routing between them using tagged sub-interfaces, one gateway per VLAN.

```mermaid
graph TD
    VM2["VM2 - VLAN 10<br/>192.168.10.10"]
    VM3["VM3 - VLAN 20<br/>192.168.20.20"]
    BR["vmbr3<br/>VLAN-aware bridge"]
    VM1["VM1 - Router<br/>ens21.10→192.168.10.1 gw<br/>ens21.20→192.168.20.1 gw<br/>ip_forward enabled"]

    VM2 -->|"access port · tag 10"| BR
    VM3 -->|"access port · tag 20"| BR
    BR -->|"trunk · all tags"| VM1
```
