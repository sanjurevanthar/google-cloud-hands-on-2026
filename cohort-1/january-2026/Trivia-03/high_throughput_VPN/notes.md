# Notes & Observations

## VPN Characteristics

- The VPN uses **route-based IPsec tunnels**
- ESP + UDP/500 + UDP/4500 forwarding is required for negotiation and transport
- Both tunnels require matching secrets and matching peer addresses

## Routing Behavior

- Subnet reachability is not automatic; explicit static routes are required
- Traffic between 10.0.1.0/24 ↔ 192.168.1.0/24 flows only after routes are configured
- ICMP connectivity over VPN helps validate routing before testing throughput

## Throughput Test Insights

- VPN throughput depends on:
  - VM bandwidth caps (2 Gbps per vCPU for this machine class)
  - IKEv2 CPU load on VM
  - Number of parallel streams (`-P` flag)
- 20 streams generally saturate the tunnel more effectively than a single stream

## Troubleshooting Checklist

- Ensure `tcp/5001` allowed for iperf
- Ensure VPN tunnel status is `ESTABLISHED`
- Check that:
  - subnet prefixes do not overlap
  - routes point to correct tunnel
  - both gateways negotiated IKEv2 correctly

## Useful Verifications

- gcloud compute vpn-tunnels list
- gcloud compute forwarding-rules list
- gcloud compute routes list
- gcloud compute addresses list


## Key Takeaways

- A single tunnel can be upgraded by increasing VM throughput or adding parallel tunnels
- IPsec VPN is viable for hybrid connectivity but may hit performance ceilings for large data transfers
- Full mesh VPN between regions increases redundancy for hybrid workloads
