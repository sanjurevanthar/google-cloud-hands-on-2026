# Commands Executed

## 1. Create VPC Networks

gcloud compute networks create cloud --subnet-mode=custom
gcloud compute networks create on-prem --subnet-mode=custom

## 2. Add Firewall Rules (SSH + ICMP + iperf)

gcloud compute firewall-rules create cloud-fw --network cloud --allow tcp:22,tcp:5001,udp:5001,icmp
gcloud compute firewall-rules create on-prem-fw --network on-prem --allow tcp:22,tcp:5001,udp:5001,icmp

## 3. Create Subnets

gcloud compute networks subnets create cloud-east
--network cloud --range 10.0.1.0/24 --region <REGION2>

gcloud compute networks subnets create on-prem-central
--network on-prem --range 192.168.1.0/24 --region <REGION1>

## 4. Create VPN Gateways

gcloud compute target-vpn-gateways create cloud-gw1 --network cloud --region <REGION2>
gcloud compute target-vpn-gateways create on-prem-gw1 --network on-prem --region <REGION1>

## 5. Allocate External IPs for Gateways

gcloud compute addresses create cloud-gw1 --region <REGION2>
gcloud compute addresses create on-prem-gw1 --region <REGION1>

Store IP addresses:

cloud_gw1_ip=$(gcloud compute addresses describe cloud-gw1 --region <REGION2> --format='value(address)')
on_prem_gw_ip=$(gcloud compute addresses describe on-prem-gw1 --region <REGION1> --format='value(address)')

## 6. Create Forwarding Rules for IPsec

### Cloud side:

gcloud compute forwarding-rules create cloud-fr-esp --ip-protocol ESP --address $cloud_gw1_ip --target-vpn-gateway cloud-gw1 --region <REGION2>
gcloud compute forwarding-rules create cloud-fr-udp500 --ip-protocol UDP --ports 500 --address $cloud_gw1_ip --target-vpn-gateway cloud-gw1 --region <REGION2>
gcloud compute forwarding-rules create cloud-fr-udp4500 --ip-protocol UDP --ports 4500 --address $cloud_gw1_ip --target-vpn-gateway cloud-gw1 --region <REGION2>


### On-prem side:

gcloud compute forwarding-rules create on-prem-fr-esp --ip-protocol ESP --address $on_prem_gw_ip --target-vpn-gateway on-prem-gw1 --region <REGION1>
gcloud compute forwarding-rules create on-prem-fr-udp500 --ip-protocol UDP --ports 500 --address $on_prem_gw_ip --target-vpn-gateway on-prem-gw1 --region <REGION1>
gcloud compute forwarding-rules create on-prem-fr-udp4500 --ip-protocol UDP --ports 4500 --address $on_prem_gw_ip --target-vpn-gateway on-prem-gw1 --region <REGION1>

## 7. Create Matching VPN Tunnels

(shared secret used in both)

gcloud compute vpn-tunnels create on-prem-tunnel1
--peer-address $cloud_gw1_ip --target-vpn-gateway on-prem-gw1
--ike-version 2 --local-traffic-selector 0.0.0.0/0
--remote-traffic-selector 0.0.0.0/0 --shared-secret "sharedsecret"
--region <REGION1>

gcloud compute vpn-tunnels create cloud-tunnel1
--peer-address $on_prem_gw_ip --target-vpn-gateway cloud-gw1
--ike-version 2 --local-traffic-selector 0.0.0.0/0
--remote-traffic-selector 0.0.0.0/0 --shared-secret "sharedsecret"
--region <REGION2>

## 8. Add Routes for Subnets

gcloud compute routes create on-prem-route1
--destination-range 10.0.1.0/24 --network on-prem
--next-hop-vpn-tunnel on-prem-tunnel1 --next-hop-vpn-tunnel-region <REGION1>

gcloud compute routes create cloud-route1
--destination-range 192.168.1.0/24 --network cloud
--next-hop-vpn-tunnel cloud-tunnel1 --next-hop-vpn-tunnel-region <REGION2>

## 9. Create Load Test VMs

gcloud compute instances create cloud-loadtest --zone <ZONE2>
--machine-type e2-standard-4 --subnet cloud-east --image-family debian-11 --image-project debian-cloud

gcloud compute instances create on-prem-loadtest --zone <ZONE1>
--machine-type e2-standard-4 --subnet on-prem-central --image-family debian-11 --image-project debian-cloud

## 10. Install `iperf`

sudo apt-get install iperf

## 11. Run Throughput Test

(on-prem) iperf -s -i 5
(cloud) iperf -c 192.168.1.2 -P 20 -x C