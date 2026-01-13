# Multiple VPC Networks – Connectivity Exploration on Google Cloud

## Overview
This exercise demonstrates how isolated Virtual Private Cloud (VPC) networks behave when multiple networks, subnets, firewall rules, and virtual machines are introduced in a cloud environment.  

The focus is on inspecting how traffic moves between networks and how routing, firewall rules, and multi-NIC virtual machines influence connectivity outcomes.

A key part of the exercise is creating:
- Two custom VPC networks (`managementnet` and `privatenet`)
- Firewall rules allowing ICMP, SSH, and RDP ingress
- VM instances distributed across networks and zones
- A VM with multiple network interfaces to bridge networks
- Connectivity tests using internal and external IPs

## Concepts Demonstrated
This lab reinforces several foundational GCP networking concepts:

### **VPC Networking**
- VPC networks form isolated private networks inside a cloud project
- VPCs support both auto and custom subnet creation approaches
- Subnets are regional resources

### **Firewall Rules**
- Rules evaluate traffic before VM network interfaces
- Direction, action, protocol, and priority influence packet handling
- ICMP rules allow pinging and diagnostic connectivity

### **Internal vs External Connectivity**
- External IPs allow public reachability when permitted by rules
- Internal IP reachability requires networks to be part of the same VPC unless peering or VPN is introduced

### **Routing Behavior**
- Subnet presence determines directly connected routes
- Default routes influence cross-network egress behavior
- Multi-NIC VM instances deploy multiple routing tables

### **Multi-NIC Instances**
- Instances can attach up to 8 interfaces depending on machine type
- Each NIC maps to a separate VPC network + subnet CIDR block
- Enables network bridging patterns for appliance-style workloads

## Network Architecture
The final topology includes:

- `mynetwork` (auto mode) — pre-existing
- `managementnet` (custom)
- `privatenet` (custom)

VM deployment matrix:

| Instance | Network | Subnet | Interface Count |
|---------|---------|--------|-----------------|
| mynet-vm-1 | mynetwork | auto | 1 |
| mynet-vm-2 | mynetwork | auto | 1 |
| managementnet-vm-1 | managementnet | managementsubnet-1 | 1 |
| privatenet-vm-1 | privatenet | privatesubnet-1 | 1 |
| vm-appliance | privatenet + managementnet + mynetwork | multiple | 3 |

## Objectives Achieved
By the end of this lab the following outcomes were validated:

- Created and configured custom VPC networks
- Attached firewall rules to permit diagnostic connectivity
- Launched VM instances in multiple networks and subnets
- Verified public reachability using external IPs
- Verified network isolation using internal IP tests
- Demonstrated cross-network visibility with multi-NIC routing
- Explored default route behavior inside Linux using `ip route`

## Practical Takeaways
Key observations include:

1. VPC networks are isolated by default
2. External IP access does not imply internal connectivity
3. Internal DNS resolves VM names to primary NICs
4. Multi-NIC instances require explicit routing for full mesh communication
5. Lack of connectivity is often due to:
   - missing firewall rules
   - subnet routing boundaries
   - default route precedence

## Deployment Model
This lab uses:
- Google Compute Engine for VM resources
- VPC Networks and subnet resources for segmentation
- Firewall rules for ingress control

## Related Topics / Further Exploration
Recommended adjacent topics include:
- VPC Peering
- Shared VPC
- Cloud VPN / Interconnect
- Cloud Router (BGP dynamic routing)
- Policy routing / Traffic selectors

---
Repository artifacts for this exercise:

- `commands.md` — all CLI commands used during the lab
- `notes.md` — observations and learning notes

