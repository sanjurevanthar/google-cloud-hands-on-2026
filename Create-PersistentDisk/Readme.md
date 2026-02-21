# Creating a Persistent Disk on Google Compute Engine

## Overview

This lab walks you through creating a **persistent disk** in Google Cloud, attaching it to a virtual machine, formatting it, and mounting it for use. Persistent disks are the primary block storage option for Compute Engine VMs and exist independently of the VMs they are attached to.

## What You Will Do

- Create a Compute Engine VM instance
- Create a 200 GB persistent disk in the same zone
- Attach the persistent disk to the VM
- Format and mount the disk inside the VM
- Configure the disk to auto-mount on every restart

## Outcome

By the end of this lab, you will have:

- A running VM (`gcelab`) with a boot disk and an additional persistent disk (`mydisk`)
- A formatted and mounted 200 GB disk accessible at `/mnt/mydisk`
- A persistent mount configuration so the disk survives VM restarts

## Where This Applies

- Setting up additional storage for applications or databases on cloud VMs
- Separating OS storage from data storage for cleaner architecture
- Migrating data between VMs using detachable disks
- Any scenario where data must survive VM deletion or restarts

## Real-World Use Cases

- A database server that needs dedicated, high-capacity storage separate from the OS disk
- A development environment where work is stored on a disk that can be moved to a new VM
- A media or file server that stores large amounts of user data on expandable persistent storage
- A backup solution where disk snapshots are taken regularly for disaster recovery