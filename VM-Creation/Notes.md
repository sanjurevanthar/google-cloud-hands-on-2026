# Notes.md

## What is a Virtual Machine?

A Virtual Machine (VM) is basically a computer inside a computer. It behaves like a real computer — it has an operating system, storage, and runs programs — but it lives on someone else's physical hardware. You don't need to buy or manage any physical servers.

## What is Google Compute Engine?

Google Compute Engine (GCE) is Google Cloud's service that lets you create and run VMs. You choose how powerful the VM should be, what operating system it runs, and where in the world it lives. Google handles the physical hardware for you.

## Regions and Zones

Google Cloud's infrastructure is spread across the globe, organized into **regions** and **zones**.

- A **region** is a geographic location, like `us-central1` (Central United States).
- A **zone** is a smaller area inside a region, like `us-central1-a`. Think of it like a specific building inside a city.
- VMs and their storage disks must be in the **same zone** to work together.
- Static IP addresses must be in the **same region** as the VM they are assigned to.

## Machine Types

A machine type tells GCE how much CPU and memory (RAM) to give your VM.

- The **E2 series** is a cost-friendly, general-purpose option — good for most everyday tasks.
- **e2-medium** gives you 2 CPUs and 4 GB of RAM, which is enough for a basic web server or development work.

## Boot Disks

Every VM needs a boot disk — this is where the operating system is stored. When creating a VM, you pick:

- The **operating system** (e.g., Debian, Ubuntu, Windows)
- The **disk type** — Balanced is the default and works well for most use cases
- The **disk size** — 10 GB is enough for basic setups

## Networking and Firewall Rules

VMs are connected to a **Virtual Private Cloud (VPC)** network. By default, most external traffic is blocked for security.

- A **firewall rule** opens specific ports so traffic can reach your VM.
- Allowing **HTTP traffic** opens port 80, which lets people visit your web server from a browser.
- Every VM gets an **Internal IP** (used inside Google Cloud) and an **External IP** (used to reach it from the internet).

## NGINX Web Server

NGINX is a popular, lightweight web server. It's often used to serve websites, handle API traffic, or act as a reverse proxy. Installing NGINX on a VM is one of the simplest ways to host something on the web.

## Two Ways to Create VMs

1. **Cloud Console (GUI)** — A point-and-click web interface. Great for beginners or one-time setups.
2. **gcloud CLI** — A command-line tool that lets you create and manage resources by typing commands. Faster for repeated tasks and easy to automate.

**Cloud Shell** is a free, browser-based terminal that comes pre-installed with `gcloud` and is already connected to your Google Cloud account — no setup needed.

## Key Terms at a Glance

| Term | What it means |
|---|---|
| VM Instance | A virtual computer running on Google's infrastructure |
| Region | A geographic location (e.g., us-central1) |
| Zone | A specific area within a region (e.g., us-central1-a) |
| Machine Type | The CPU and RAM size of your VM |
| Boot Disk | The storage drive that holds the operating system |
| Firewall Rule | A rule that allows or blocks network traffic |
| External IP | The public address used to reach your VM from the internet |
| gcloud | Google Cloud's command-line tool |
| Cloud Shell | A browser-based terminal for running gcloud commands |
| NGINX | A popular open-source web server |