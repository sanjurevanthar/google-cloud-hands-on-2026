# commands.md

## Part 1 – Set the Region and Zone

```bash
gcloud config set compute/region REGION
```

```bash
export REGION=REGION
```

```bash
export ZONE=Zone
```

---

## Part 2 – Create a VM via the Cloud Console (GUI Steps)

1. Go to **Navigation Menu (☰) > Compute Engine > VM Instances**
2. Click **Create Instance**
3. Fill in the fields under **Machine configuration**:

| Field | Value |
|---|---|
| Name | `gcelab` |
| Region | `<REGION>` |
| Zone | `<ZONE>` |
| Series | `E2` |
| Machine Type | `e2-medium` |

4. Click **OS and storage** → Click **Change** and set:
   - Operating system: `Debian`
   - Version: `Debian GNU/Linux 12 (bookworm)`
   - Boot disk type: `Balanced persistent disk`
   - Size: `10 GB`

5. Click **Networking** → Under **Firewall**, check **Allow HTTP traffic**
6. Click **Create**
7. Once running, click **SSH** next to `gcelab` to open a terminal

---

## Part 3 – Install NGINX on the VM

Run these commands inside the SSH session on `gcelab`:

```bash
sudo apt-get update
```

```bash
sudo apt-get install -y nginx
```

```bash
ps auwx | grep nginx
```

To verify: open a browser and go to `http://<EXTERNAL_IP>/`

---

## Part 4 – Create a Second VM Using gcloud

Run in **Cloud Shell**:

```bash
gcloud compute instances create gcelab2 --machine-type e2-medium --zone=$ZONE
```

To view all available creation options:

```bash
gcloud compute instances create --help
```

To set a default zone and region globally (optional):

```bash
gcloud config set compute/zone <ZONE>
```

```bash
gcloud config set compute/region <REGION>
```

---

## Part 5 – SSH into gcelab2 via gcloud

```bash
gcloud compute ssh gcelab2 --zone=$ZONE
```

- Type `Y` and press **Enter** when prompted
- Press **Enter** twice to skip the passphrase

To disconnect:

```bash
exit
```