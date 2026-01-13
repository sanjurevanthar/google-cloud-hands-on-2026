# Commands Used in Multi-VPC Networking Exercise

## List Active Project
```bash
gcloud config list project
```

## Create Custom VPC Networks
```bash
gcloud compute networks create privatenet --subnet-mode=custom
gcloud compute networks create managementnet --subnet-mode=custom
```

## Create Subnets
```bash
gcloud compute networks subnets create privatesubnet-1 \
  --network=privatenet \
  --region=<REGION_1> \
  --range=172.16.0.0/24

gcloud compute networks subnets create privatesubnet-2 \
  --network=privatenet \
  --region=<REGION_2> \
  --range=172.20.0.0/20
```

Management subnet was created via console in the lab but equivalent CLI:
```bash
gcloud compute networks subnets create managementsubnet-1 \
  --network=managementnet \
  --region=<REGION_1> \
  --range=10.130.0.0/20
```

## Firewall Rules
```bash
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp \
  --direction=INGRESS \
  --priority=1000 \
  --network=privatenet \
  --action=ALLOW \
  --rules=icmp,tcp:22,tcp:3389 \
  --source-ranges=0.0.0.0/0
```

Equivalent management rule (console-based originally):
```bash
gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp \
  --direction=INGRESS \
  --priority=1000 \
  --network=managementnet \
  --action=ALLOW \
  --rules=icmp,tcp:22,tcp:3389 \
  --source-ranges=0.0.0.0/0
```

## VM Creation
Privatenet:
```bash
gcloud compute instances create privatenet-vm-1 \
  --zone=<ZONE_1> \
  --machine-type=e2-micro \
  --subnet=privatesubnet-1
```

Managementnet (console in lab, CLI equivalent):
```bash
gcloud compute instances create managementnet-vm-1 \
  --zone=<ZONE_1> \
  --machine-type=e2-micro \
  --subnet=managementsubnet-1
```

Multi-interface appliance VM:
```bash
gcloud compute instances create vm-appliance \
  --zone=<ZONE_1> \
  --machine-type=e2-standard-4 \
  --network-interface=network=privatenet,subnet=privatesubnet-1 \
  --network-interface=network=managementnet,subnet=managementsubnet-1 \
  --network-interface=network=mynetwork,subnet=mynetwork
```

## Connectivity Tests Inside VM
ICMP tests:
```bash
ping -c 3 <TARGET-IP>
```

List network interfaces:
```bash
sudo ifconfig
```

Routing table inspection:
```bash
ip route
```

## Resource Listing
```bash
gcloud compute networks list
gcloud compute networks subnets list --sort-by=NETWORK
gcloud compute firewall-rules list --sort-by=NETWORK
gcloud compute instances list --sort-by=ZONE
```

