# Notes.md

## What is a Persistent Disk?

A persistent disk is like a hard drive for your virtual machine in Google Cloud. Just like a USB drive you plug into a laptop, you can attach a persistent disk to a VM to give it more storage. The key thing about persistent disks is that they are **independent** from the VM — if you delete the VM, the disk (and all its data) still exists. You can then attach it to a different VM.

## Types of Persistent Disks

Google Cloud offers two main types of persistent disks:

- **Standard Persistent Disk** — Uses regular hard disk drive (HDD) technology. Cheaper, but slower. Good for storing large amounts of data that doesn't need to be read or written quickly.
- **SSD Persistent Disk** — Uses solid-state drive technology. Faster and more responsive, but costs more. Good for databases or applications that need quick access to data.

Each type has different limits on how large it can be, so always check the documentation when planning storage for production workloads.

## How Persistent Disks Differ from Local SSDs

It's important to understand the difference between persistent disks and **Local SSDs**:

| Feature | Persistent Disk | Local SSD |
|---|---|---|
| Attached to | Network (not physically) | Physically to the server |
| Speed | Good | Very fast (sub-1ms latency) |
| Data on VM delete | Survives | Lost |
| Automatic replication | Yes | No |
| Use case | General storage, databases | High-performance, temporary data |

Local SSDs are extremely fast but risky — data can be lost if the host has an error. Persistent disks are safer because they are replicated automatically.

## Regions and Zones (Storage Context)

When you create a persistent disk, it must be in the **same zone** as the VM you want to attach it to. A disk in `us-central1-a` cannot be attached to a VM in `us-central1-b`. This is a common gotcha when setting up storage in GCP.

## What Happens When You Attach a Disk?

When you attach a persistent disk to a VM, the VM sees it as a **block device** — similar to how a computer sees a new hard drive. The disk shows up in the file system under a path like `/dev/disk/by-id/`. However, before you can use it, you need to:

1. **Format the disk** — Set up a file system on it (just like formatting a new hard drive)
2. **Mount the disk** — Tell the operating system where to make the disk accessible (e.g., `/mnt/mydisk`)

## Formatting a Disk

Formatting creates a file system on the disk so it can store files. A common file system used on Linux is **ext4**. The tool used for this is `mkfs` (make file system). Be careful — formatting a disk **deletes all existing data** on it.

## Mounting a Disk

Mounting means connecting the disk to a specific folder on your VM so you can read and write files to it. The tool used is `mount`. You can pick any folder as the **mount point** (a common choice is `/mnt/mydisk`).

## Auto-Mount on Restart

By default, if your VM restarts, the disk will not automatically reconnect. To fix this, you add an entry to a special file called `/etc/fstab`. This file tells Linux which disks to mount automatically every time the system boots.

## Why Use Persistent Disks?

- **Data survives VM deletion** — Great for databases and important files
- **Flexible sizing** — You can create disks from a few GB up to 64 TB
- **Shareable** — A disk can be detached from one VM and attached to another
- **Snapshots** — You can take point-in-time backups of persistent disks and restore or migrate them to other regions

## Migrating Data from a Persistent Disk to Another Region

If you need to move data from a persistent disk to a different region, you need to follow a specific order of steps. Doing them out of order can cause data loss or a corrupted snapshot. Here is the correct order:

### Correct Order: 5 → 3 → 2 → 4 → 1

**Step 5 – Unmount the File System(s)**
Before doing anything else, you must safely disconnect the disk from the VM's file system. This makes sure no files are being written to the disk at the moment you copy it. Skipping this step can result in a snapshot that has incomplete or corrupted data.

**Step 3 – Create a Snapshot**
Once the disk is safely unmounted, you take a snapshot of it. A snapshot is a full copy of the disk's data at that exact moment. Snapshots in GCP are global resources, which means they can be used to create disks in any region — that's what makes them the key tool for cross-region migration.

**Step 2 – Create a Disk**
Using the snapshot you just created, you create a new persistent disk in the **target region**. This new disk is an exact copy of the original, now living in the new location where you need it.

**Step 4 – Create an Instance**
Now you create a new VM instance in the target region. This is the machine that will use the new disk. It's important to create the instance in the same zone as the new disk.

**Step 1 – Attach the Disk**
Finally, attach the newly created disk to the new VM instance. Once attached, you can mount it inside the VM and continue working as normal — now in the new region.

### Why This Order Matters

| Step | Action | Why |
|---|---|---|
| 5 | Unmount file system(s) | Ensures no data is being written — prevents corruption |
| 3 | Create snapshot | Captures a clean, complete copy of the disk |
| 2 | Create disk | Builds a new disk from the snapshot in the target region |
| 4 | Create instance | Sets up the VM in the new region to receive the disk |
| 1 | Attach disk | Connects the new disk to the new VM — migration complete |

---

## Key Terms at a Glance

| Term | What it means |
|---|---|
| Persistent Disk | A network-attached storage drive for GCE VMs |
| Block Device | How the OS sees an attached disk (like `/dev/sdb`) |
| Mount Point | The folder where a disk is made accessible |
| ext4 | A common Linux file system format |
| mkfs | Linux tool to format a disk with a file system |
| mount | Linux tool to attach a formatted disk to a folder |
| /etc/fstab | Config file that controls auto-mounting on boot |
| Local SSD | A physically attached, ultra-fast but non-persistent disk |
| Snapshot | A backup copy of a persistent disk |