# commands.md

## Part 1 – Set Project IDs in Each Cloud Shell

In the Cloud Shell for **Project A**:
```bash
gcloud config set project PROJECT_ID_1
```

In the Cloud Shell for **Project B**:
```bash
gcloud config set project PROJECT_ID_2
```

---

## Part 2 – Create Custom Network in Project A

Create the VPC network:
```bash
gcloud compute networks create network-a --subnet-mode custom
```

Create a subnet inside the network:
```bash
gcloud compute networks subnets create network-a-subnet --network network-a \
    --range 10.0.0.0/16 --region "REGION 1"
```

Create a VM instance:
```bash
gcloud compute instances create vm-a --zone "ZONE 1" --network network-a --subnet network-a-subnet --machine-type e2-small
```

Create a firewall rule to allow SSH and ICMP:
```bash
gcloud compute firewall-rules create network-a-fw --network network-a --allow tcp:22,icmp
```

---

## Part 3 – Create Custom Network in Project B

Create the VPC network:
```bash
gcloud compute networks create network-b --subnet-mode custom
```

Create a subnet inside the network:
```bash
gcloud compute networks subnets create network-b-subnet --network network-b \
    --range 10.8.0.0/16 --region "REGION 2"
```

Create a VM instance:
```bash
gcloud compute instances create vm-b --zone "ZONE 2" --network network-b --subnet network-b-subnet --machine-type e2-small
```

Create a firewall rule to allow SSH and ICMP:
```bash
gcloud compute firewall-rules create network-b-fw --network network-b --allow tcp:22,icmp
```

---

## Part 4 – Set Up VPC Network Peering (GUI Steps)

### Peer network-a → network-b (Project A side)

1. In **Project A** Console, go to **Navigation Menu > VPC Network > VPC network peering**
2. Click **Create connection** → Click **Continue**
3. Fill in the fields:

| Field | Value |
|---|---|
| Name | `peer-ab` |
| Your VPC network | `network-a` |
| Peered VPC network | `In another project` |
| Project ID | `PROJECT_ID_2` |
| VPC network name | `network-b` |

4. Click **Create**

> Status will show **INACTIVE** — waiting for the other side to connect

---

### Peer network-b → network-a (Project B side)

1. Switch to **Project B** Console, go to **Navigation Menu > VPC Network > VPC network peering**
2. Click **Create connection** → Click **Continue**
3. Fill in the fields:

| Field | Value |
|---|---|
| Name | `peer-ba` |
| Your VPC network | `network-b` |
| Peered VPC network | `In another project` |
| Project ID | `PROJECT_ID_1` |
| VPC network name | `network-a` |

4. Click **Create**

> Status will now change to **ACTIVE** — peering is established

---

## Part 5 – View Peering Routes (Project A)

```bash
gcloud compute routes list --project PROJECT_ID_1
```

---

## Part 6 – Test Connectivity

In **Project A** Console:
1. Go to **Navigation Menu > Compute Engine > VM instances**
2. Copy the **INTERNAL_IP** of `vm-a`

In **Project B** Console:
1. Go to **Navigation Menu > Compute Engine > VM instances**
2. Click **SSH** next to `vm-b`

Inside the SSH session on `vm-b`, ping `vm-a` using its internal IP:
```bash
ping -c 5 <INTERNAL_IP_OF_VM_A>
```