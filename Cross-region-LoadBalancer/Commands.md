# commands.md

## Part 1 – Create VM Instances in Region 1

Create instance `www-1`:
```bash
gcloud compute instances create www-1 \
    --image-family debian-11 \
    --image-project debian-cloud \
    --machine-type e2-micro \
    --zone ZONE \
    --tags http-tag \
    --metadata startup-script="#! /bin/bash
      apt-get update
      apt-get install apache2 -y
      service apache2 restart"
```

Create instance `www-2`:
```bash
gcloud compute instances create www-2 \
    --image-family debian-11 \
    --image-project debian-cloud \
    --machine-type e2-micro \
    --zone ZONE \
    --tags http-tag \
    --metadata startup-script="#! /bin/bash
      apt-get update
      apt-get install apache2 -y
      service apache2 restart"
```

---

## Part 2 – Create VM Instances in Region 2

Create instance `www-3`:
```bash
gcloud compute instances create www-3 \
    --image-family debian-11 \
    --image-project debian-cloud \
    --machine-type e2-micro \
    --zone ZONE_2 \
    --tags http-tag \
    --metadata startup-script="#! /bin/bash
      apt-get update
      apt-get install apache2 -y
      service apache2 restart"
```

Create instance `www-4`:
```bash
gcloud compute instances create www-4 \
    --image-family debian-11 \
    --image-project debian-cloud \
    --machine-type e2-micro \
    --zone ZONE_2 \
    --tags http-tag \
    --metadata startup-script="#! /bin/bash
      apt-get update
      apt-get install apache2 -y
      service apache2 restart"
```

---

## Part 3 – Create Firewall Rule (Allow All HTTP for Testing)

```bash
gcloud compute firewall-rules create www-firewall \
    --target-tags http-tag --allow tcp:80
```

Verify instances are running and get their external IPs:
```bash
gcloud compute instances list
```

---

## Part 4 – Configure Services for Load Balancing

Reserve a global static IPv4 address:
```bash
gcloud compute addresses create lb-ip-cr \
    --ip-version=IPV4 \
    --global
```

Create unmanaged instance groups for each zone:
```bash
gcloud compute instance-groups unmanaged create REGION-resources-w --zone ZONE
```

```bash
gcloud compute instance-groups unmanaged create REGION_2-resources-w --zone ZONE_2
```

Add instances to each instance group:
```bash
gcloud compute instance-groups unmanaged add-instances REGION-resources-w \
    --instances www-1,www-2 \
    --zone ZONE
```

```bash
gcloud compute instance-groups unmanaged add-instances REGION_2-resources-w \
    --instances www-3,www-4 \
    --zone ZONE_2
```

Create a health check:
```bash
gcloud compute health-checks create http http-basic-check
```

---

## Part 5 – Configure the Load Balancing Service

Set named ports on each instance group:
```bash
gcloud compute instance-groups unmanaged set-named-ports REGION-resources-w \
    --named-ports http:80 \
    --zone ZONE
```

```bash
gcloud compute instance-groups unmanaged set-named-ports REGION_2-resources-w \
    --named-ports http:80 \
    --zone ZONE_2
```

Create the backend service:
```bash
gcloud compute backend-services create web-map-backend-service \
    --protocol HTTP \
    --health-checks http-basic-check \
    --global
```

Add Region 1 instance group as a backend:
```bash
gcloud compute backend-services add-backend web-map-backend-service \
    --balancing-mode UTILIZATION \
    --max-utilization 0.8 \
    --capacity-scaler 1 \
    --instance-group REGION-resources-w \
    --instance-group-zone ZONE \
    --global
```

Add Region 2 instance group as a backend:
```bash
gcloud compute backend-services add-backend web-map-backend-service \
    --balancing-mode UTILIZATION \
    --max-utilization 0.8 \
    --capacity-scaler 1 \
    --instance-group REGION_2-resources-w \
    --instance-group-zone ZONE_2 \
    --global
```

Create a URL map:
```bash
gcloud compute url-maps create web-map \
    --default-service web-map-backend-service
```

Create a target HTTP proxy:
```bash
gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map
```

Look up the reserved static IP address:
```bash
gcloud compute addresses list
```

Create the global forwarding rule (replace `[LB_IP_ADDRESS]` with your static IP):
```bash
gcloud compute forwarding-rules create http-cr-rule \
    --address [LB_IP_ADDRESS] \
    --global \
    --target-http-proxy http-lb-proxy \
    --ports 80
```

---

## Part 6 – Send Traffic and Verify

List all forwarding rules to find your load balancer IP:
```bash
gcloud compute forwarding-rules list
```

- In the Console, go to **Load Balancing** and verify the `web-map` load balancer shows a green checkmark
- Check the **Backend** section to confirm all instances are **Healthy**
- Copy the **Frontend IP** and paste it in your browser — you should see a web page from one of your instances
- Reload the page multiple times to see responses alternating between `www-1` and `www-2` (or `www-3` and `www-4` depending on your location)

---

## Part 7 – Lock Down Firewall to Load Balancer Only

Create a new firewall rule allowing only load balancer and health check traffic:
```bash
gcloud compute firewall-rules create allow-lb-and-healthcheck \
    --source-ranges 130.211.0.0/22,35.191.0.0/16 \
    --target-tags http-tag \
    --allow tcp:80
```

Delete the old open firewall rule:
```bash
gcloud compute firewall-rules delete www-firewall
```

Verify the load balancer still works (paste load balancer IP in browser — should work):
```bash
gcloud compute addresses list
```

Verify direct VM access is blocked (paste a VM's external IP in browser — should fail):
```bash
gcloud compute instances list
```

---

## Part 8 – (Optional) Remove External IPs from Backend VMs

List instances to find their names:
```bash
gcloud compute instances list
```

Delete the external IP access config for an instance (replace `NAME` with the instance name):
```bash
gcloud compute instances delete-access-config NAME
```