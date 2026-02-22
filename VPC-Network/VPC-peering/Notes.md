# Notes.md

## What is a Network?

Before understanding VPC, it helps to understand what a network is. A **network** is a group of computers or devices that are connected together so they can share information. Your home Wi-Fi is a small network — your phone, laptop, and TV can all talk to each other and share the internet connection.

In cloud computing, networks work the same way but at a much larger scale — instead of home devices, you're connecting virtual machines, databases, and services running in data centers around the world.

---

## What is a VPC (Virtual Private Cloud)?

A **Virtual Private Cloud (VPC)** is your own private, isolated section of Google Cloud's network. Think of it like renting a floor in a large office building. The building (Google's infrastructure) is shared by many tenants, but your floor is completely private — only your people can access it, and it's separated from everyone else's floor by walls and locks.

A VPC lets you:
- Define your own **IP address ranges** for your resources
- Control which resources can talk to each other
- Control what traffic can come in from or go out to the internet
- Keep your resources completely private and hidden from the public internet if you choose

Every VM, database, or service you create in GCP lives inside a VPC network. Without a VPC, your resources would have nowhere to live on the network.

### VPC is Global
In Google Cloud, a VPC network is **global** — it spans all regions. However, the **subnets** inside a VPC are regional, meaning each subnet is tied to a specific geographic location.

---

## What is a Subnet?

A **subnet** (short for subnetwork) is a smaller section of a VPC network. If a VPC is the whole office building, a subnet is one specific room or department on a floor.

Each subnet:
- Belongs to a specific **region** (e.g., `us-central1`)
- Has its own **IP address range** (e.g., `10.0.0.0/16`)
- Any VM created in that subnet gets an IP address from that range

You can have multiple subnets in one VPC, each in different regions, each with different IP ranges.

### What is an IP Range (CIDR)?
An IP range like `10.0.0.0/16` defines a block of IP addresses. The `/16` tells you how many addresses are in the block — the smaller the number after the slash, the more addresses are available. `10.0.0.0/16` gives you about 65,000 possible IP addresses to assign to resources in that subnet.

---

## What are Firewall Rules?

A **firewall rule** is a security gate for your network. It decides what traffic is allowed in or out of your VPC.

- Without firewall rules, traffic is blocked by default
- You can allow specific types of traffic — for example, allowing `SSH (port 22)` lets you remotely connect to a VM using a terminal
- **ICMP** is the protocol used by the `ping` command — allowing it lets you test connectivity between VMs
- Firewall rules apply to an entire network or specific VMs using tags

---

## What is VPC Network Peering?

**VPC Network Peering** is a way to connect two separate VPC networks so they can communicate with each other using **private internal IP addresses** — no public internet involved.

Imagine two office buildings (two VPC networks). Normally, if someone from Building A needs to visit Building B, they have to go outside, walk through the street (the public internet), and enter through the front door. VPC Peering is like building a **private tunnel or bridge** directly between the two buildings — no need to go outside at all.

### Why Use Peering Instead of Public IPs?

| Without Peering | With Peering |
|---|---|
| Traffic goes over the public internet | Traffic stays on Google's private network |
| Higher latency | Lower latency |
| Exposed to internet security risks | Fully private and secure |
| Higher cost (egress charges) | Lower cost (internal IP traffic) |

---

## When Would You Use VPC Network Peering?

VPC Network Peering is useful in several real-world scenarios:

- **Multiple projects in one organization** — A company might have separate GCP projects for its development team, finance team, and operations team. Peering lets these isolated networks communicate privately without going over the internet.
- **Mergers and acquisitions** — When two companies merge, they may have separate GCP organizations. Peering lets them connect networks without restructuring everything.
- **SaaS providers** — A software company can offer its services privately to customers' GCP networks through peering, instead of exposing services to the public internet.
- **Third-party service access** — You can peer with a partner organization so your VMs can access their services privately and securely.

---

## How VPC Network Peering Works

Peering is **not one-sided** — it requires both networks to agree and configure the connection. Think of it like a handshake: both parties have to reach out at the same time.

Here's how it works step by step:

**Step 1 — Network A reaches out to Network B:**
The admin of Network A creates a peering configuration that says: "I want to peer with Network B in Project B." At this point, the status is **INACTIVE** — the handshake is only halfway done.

**Step 2 — Network B reaches out to Network A:**
The admin of Network B creates a matching peering configuration that says: "I want to peer with Network A in Project A."

**Step 3 — Peering becomes ACTIVE:**
As soon as both sides have configured the peering, Google Cloud activates the connection. Routes are automatically exchanged between the two networks — meaning each network now knows how to reach the other's IP addresses.

### Route Exchange
When peering becomes active, **implicit routes** are created automatically. This means:
- Network A learns the IP range of Network B (e.g., `10.8.0.0/16`)
- Network B learns the IP range of Network A (e.g., `10.0.0.0/16`)
- VMs in either network can now reach each other using internal IPs

---

## Important Rules and Limitations of VPC Peering

- **No overlapping IP ranges** — The IP ranges of the two networks must not overlap. If both use `10.0.0.0/16`, peering will fail because there would be no way to tell the addresses apart.
- **Peering is non-transitive** — If Network A is peered with Network B, and Network B is peered with Network C, Network A **cannot** automatically reach Network C. You would need to set up a separate peering between A and C.
- **Both sides must configure** — Peering only becomes active when both network admins set up their side of the connection.
- **Works across projects and organizations** — You only need the Project ID of the other project to set up peering.

---

## What is the ping Command?

`ping` is a basic network tool used to test whether one computer can reach another. It sends small packets of data (using the ICMP protocol) and measures how long it takes to get a response.

```bash
ping -c 5 <IP_ADDRESS>
```

- `-c 5` means send 5 packets
- If you get responses back, the two machines can communicate
- The time shown (in milliseconds) tells you how fast the connection is

In this lab, `ping` is used to verify that `vm-b` in Project B can reach `vm-a` in Project A through the VPC peering connection.

---

## Custom vs Auto Mode Networks

When creating a VPC, you choose between two modes:

- **Auto mode** — Google automatically creates subnets in every region for you with predefined IP ranges. Easy to set up but less control.
- **Custom mode** — You manually create subnets and define exactly which regions and IP ranges you want. More work, but gives you full control. This is what's used in this lab.

---

## Key Terms at a Glance

| Term | What it means |
|---|---|
| VPC | Your private isolated network in Google Cloud |
| Subnet | A smaller section of a VPC, tied to a specific region |
| IP Range (CIDR) | A block of IP addresses assigned to a subnet |
| Firewall Rule | A rule that controls what traffic is allowed in/out of a network |
| SSH (port 22) | A protocol for securely connecting to a VM via terminal |
| ICMP | The protocol used by the `ping` command to test connectivity |
| VPC Peering | A private connection between two VPC networks |
| Peering State | Either INACTIVE (one side configured) or ACTIVE (both sides configured) |
| Route | Instructions that tell the network how to reach a specific IP range |
| Implicit Route | A route automatically created when peering becomes active |
| Non-transitive | Peering does not pass through — A↔B and B↔C does not mean A↔C |
| Internal IP | A private IP address used for communication within or between peered networks |
| Project ID | A unique identifier for a GCP project, used to set up cross-project peering |
