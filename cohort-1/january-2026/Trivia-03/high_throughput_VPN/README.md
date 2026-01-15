# High-Throughput VPN Using Google Cloud

This project documents the process of building a route-based IPsec VPN between two isolated networks on Google Cloud to simulate an on-premises to cloud VPN scenario. The experiment demonstrates tunnel creation, routing, gateway configuration, and throughput testing using `iperf`.

## Purpose

Secure workload communication between Google Cloud and external environments is common in hybrid deployments. A single tunnel may not always provide sufficient throughput, so distributing traffic across multiple tunnels or scaling horizontally becomes necessary. This exercise focuses on:

- Building VPN gateways on two independent networks
- Establishing IPsec forwarding rules and tunnels
- Configuring routing between subnets
- Testing throughput performance between both ends

## Topology Simulated

+-------------------+ IPsec VPN +----------------------+
| "On-Prem" VPC | <-----------------------> | "Cloud" VPC |
| 192.168.1.0/24 | | 10.0.1.0/24 |
+-------------------+ +----------------------+
VM A VM B
(iperf server) (iperf client)


## Components Used

- **VPC Networks**
  - `on-prem`
  - `cloud`

- **VPN Gateways**
  - `on-prem-gw1`
  - `cloud-gw1`

- **VPN Tunnels**
  - `on-prem-tunnel1`
  - `cloud-tunnel1`

- **Compute Instances**
  - `on-prem-loadtest`
  - `cloud-loadtest`

- **Testing Tools**
  - `iperf` throughput test

## Deployment Flow

1. Create separate VPC networks simulating cloud and on-prem
2. Attach subnets and allow SSH / ICMP / testing ports
3. Create IPsec VPN gateways
4. Reserve external IP addresses for gateways
5. Configure forwarding rules for:
   - ESP
   - UDP/500
   - UDP/4500
6. Establish matching VPN tunnels
7. Add static routes for subnet reachability
8. Launch load test VMs
9. Run throughput test using `iperf`

## Expected Result

Once routing and tunnel negotiation succeed, both VMs should exchange traffic through the IPsec tunnel. Running `iperf` from cloud → on-prem demonstrates achievable throughput for a single tunnel.
