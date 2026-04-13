# 🚀 Azure Multi-Subnet Network with Linux Firewall

## Overview
This project demonstrates the design and implementation of a segmented virtual network in Microsoft Azure using multiple subnets and a custom Linux-based firewall.

The goal was to simulate a real-world enterprise network where traffic between subnets is controlled, routed, and analyzed through a centralized firewall system.

---

## Architecture

- **Virtual Network:** 10.0.0.0/16  
- **Subnet A (Internal):** 10.0.1.0/24  
- **Subnet B (External):** 10.0.2.0/24  

### Firewall VM (Linux)
- `eth0` → 10.0.2.0/24 (external)
- `eth1` → 10.0.1.0/24 (internal)

### Client VM (Windows)
- IP: 10.0.1.5  
- Default Gateway: 10.0.1.4 (Firewall)

All traffic from the internal subnet is routed through the firewall.

---

## Key Features

- Dual-NIC Linux VM acting as a router/firewall  
- Custom routing between Azure subnets  
- ICMP and network connectivity testing  
- Packet capture and inspection using `tcpdump`  
- Firewall configuration using `iptables` / UFW  
- Analysis of routing behavior in cloud environments  

---

## Testing & Validation

The following tests were performed:

- Verified routing tables on Linux and Windows systems  
- Tested ICMP connectivity between subnets  
- Captured traffic using `tcpdump` on the firewall  
- Confirmed packet flow at the network level  

### Example:
- ICMP requests successfully reached the firewall  
- Firewall generated valid ICMP replies  
- Traffic observed in both directions at packet level  

---

## Troubleshooting & Findings

During testing, ICMP connectivity appeared to fail despite correct routing configuration.

Deep packet inspection revealed:

- ICMP echo requests were reaching the firewall  
- The firewall generated valid echo replies  
- Duplicate ICMP packets were observed  
- ICMP redirects were being generated  
- Occasional "time exceeded" messages appeared  

### Root Cause

This behavior indicated:

- Asymmetric routing between interfaces  
- Interaction between Azure system routes and custom routing  
- Kernel-level behavior (ICMP redirects and forwarding decisions)  

This highlighted how cloud networking and OS-level routing can interact in unexpected ways, especially with multi-interface systems.

---

## Known Limitations

- ICMP responses show inconsistent behavior due to routing dynamics  
- Presence of ICMP redirects and potential routing loops  
- Routing behavior is not fully deterministic without Azure User Defined Routes (UDRs)  

---

## Future Improvements

- Implement Azure User Defined Routes (UDRs) for better control  
- Disable ICMP redirects and fine-tune kernel parameters  
- Replace UFW with `nftables` for more granular control  
- Add centralized logging and monitoring  
- Expand architecture to multi-tier (web/app/database)  

---

## Screenshots

_Add screenshots here (recommended):_

- Azure VNet and subnet configuration  
- VM network settings  
- Routing tables (`ip route`, `route print`)  
- `tcpdump` packet captures  

Example:

```bash
sudo tcpdump -i eth1 icmp
