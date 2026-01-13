# Notes & Observations – Multi-VPC Exploration

## Network Isolation
- VPC networks do not exchange internal traffic by default
- Isolation occurs even within the same region and zone

## External vs Internal Reachability
- External IP pinging succeeded for all VMs due to ICMP firewall allowance
- Internal IP pinging failed across VPC boundaries
- Internal traffic requires peering, shared VPC, or VPN to cross networks

## Multi-NIC Behavior
- `vm-appliance` could reach all three directly attached networks
- Routing table assigned default route to primary NIC (eth0)
- Non-overlapping CIDR blocks are required for multi-NIC usage

## DNS Behavior
- Internal VM hostnames resolve only for primary NIC in a VPC
- DNS does not automatically span VPC network boundaries

## Routing Notes
- Directly connected subnets receive specific routes
- Non-local traffic exits via default route unless policy routing is applied
- This explains why appliance → mynet-vm-2 failed initially

## Takeaways for Real Systems
Multi-NIC VMs are useful for:
- network appliances
- firewalls
- packet inspection systems
- load balancers
- VPN gateways
- middleboxes

But require:
- routing configuration
- security policy tuning
- peering/VPN for cross-VPC topologies

## Related Concepts to Explore
- VPC Peering
- Cloud Router + BGP
- Shared VPC
- Policy routing
- Hub-and-spoke designs
- Transit VPC patterns
