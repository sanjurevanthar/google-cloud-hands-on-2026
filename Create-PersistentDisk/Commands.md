# commands.md

## Part 1 – Set the Region and Zone

```bash
gcloud config set compute/zone Zone
gcloud config set compute/region Region
```

```bash
export REGION=Region
```

```bash
export ZONE=Zone
```

---

## Part 2 – Create a New VM Instance

```bash
gcloud compute instances create gcelab --zone $ZONE --machine-type e2-standard-2
```

---

## Part 3 – Create a New Persistent Disk

```bash
gcloud compute disks create mydisk --size=200GB \
--zone $ZONE
```

---

## Part 4 – Attach the Disk to the VM

```bash
gcloud compute instances attach-disk gcelab --disk mydisk --zone $ZONE
```

---

## Part 5 – SSH into the VM and Find the Disk

```bash
gcloud compute ssh gcelab --zone $ZONE
```

- Type `Y` and press **Enter** when prompted
- Press **Enter** twice to skip the passphrase

Once inside the VM, list the disk devices:

```bash
ls -l /dev/disk/by-id/
```

---

## Part 6 – Format and Mount the Persistent Disk

Create a mount point directory:

```bash
sudo mkdir /mnt/mydisk
```

Format the disk with ext4 file system:

```bash
sudo mkfs.ext4 -F -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/disk/by-id/scsi-0Google_PersistentDisk_persistent-disk-1
```

Mount the disk to the mount point:

```bash
sudo mount -o discard,defaults /dev/disk/by-id/scsi-0Google_PersistentDisk_persistent-disk-1 /mnt/mydisk
```

---

## Part 7 – Configure Auto-Mount on Restart

Open the fstab file in nano:

```bash
sudo nano /etc/fstab
```

Add the following line below the line that starts with `PARTUUID=...`:

```
/dev/disk/by-id/scsi-0Google_PersistentDisk_persistent-disk-1 /mnt/mydisk ext4 defaults 1 1
```

Save and exit nano:

```
CTRL+O  →  ENTER  →  CTRL+X
```