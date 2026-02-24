# Notes.md

## What is Load Balancing?

Imagine a popular restaurant with only one cashier. On a normal day, it's fine. But on a busy Friday night, there's a huge line and customers wait 30 minutes just to order. The restaurant adds three more cashiers — now the line moves fast because customers are spread across all four registers equally.

**Load balancing** works the same way for web servers. Instead of sending all user requests to one server (which can get overwhelmed), a load balancer distributes those requests across multiple servers so none of them get overloaded. The result is faster responses, less downtime, and a better experience for users.

---

## What is an HTTP Load Balancer?

An **HTTP Load Balancer** is a load balancer that works specifically with web traffic — the kind that uses the HTTP or HTTPS protocol (the same protocol your browser uses when it visits a website).

When a user opens a browser and types in a website address, their request travels as HTTP traffic. The HTTP Load Balancer sits in front of your servers and decides which server should handle each incoming request.

### What a Basic HTTP Load Balancer Does

- Accepts HTTP/HTTPS requests from users
- Distributes them across multiple servers (instances) in **one region or zone**
- Monitors server health and avoids sending traffic to unhealthy servers
- Gives you a single IP address that users connect to

Think of it like a receptionist at an office building who directs visitors to whichever staff member is available. It works great — as long as all the staff are in the same building.

---

## What is a Cross-Region Load Balancer?

A **Cross-Region Load Balancer** does everything a regular HTTP load balancer does — but it distributes traffic across servers in **multiple geographic regions** around the world.

Think of it like a global call center with offices in New York, London, and Tokyo. When a customer calls, they are automatically connected to the nearest office — the one in their time zone, where agents are available and the call quality is best. If the New York office goes down, calls automatically reroute to London or Tokyo without the customer knowing anything happened.

---

## HTTP Load Balancer vs. Cross-Region Load Balancer

This is one of the most important concepts in this lab. Here's how they compare:

| Feature | HTTP Load Balancer (Single Region) | Cross-Region Load Balancer |
|---|---|---|
| Servers location | One region or zone | Multiple regions worldwide |
| Traffic routing | Within region | To the nearest region globally |
| Best for | Apps with local users | Apps with global users |
| Fault tolerance | Limited — region failure = outage | High — if one region fails, others take over |
| Latency | Low within region | Low globally |
| Cost | Lower | Higher |

---

### Real-World Example 1 — Why a Regular HTTP Load Balancer Is Not Enough: Netflix

Netflix started as a simple streaming service with servers in the United States. When they had only American users, a regular load balancer worked fine — all requests came from nearby, and traffic was spread across servers in one region.

But as Netflix expanded to 190+ countries, a regular load balancer became a problem:
- A user in **Sydney, Australia** would send a request all the way to servers in **Virginia, USA**
- That request travels ~16,000 km round trip — causing buffering and slow load times
- If the US data center had a major outage, **every Netflix user worldwide** would lose service

Netflix solved this by building a **cross-region load balancing** architecture. Now:
- A user in Sydney connects to servers in **Australia or Asia** — close and fast
- If one region fails, traffic automatically shifts to healthy servers in another region
- Millions of users in different countries all get fast, uninterrupted streaming

---

### Real-World Example 2 — Cross-Region Load Balancing Done Right: Google Search

When you search something on Google, you might be in Lagos, Nigeria. Google doesn't send your request to a server in California. Instead, **Google's global cross-region load balancer** routes your request to the nearest Google data center — which might be in **South Africa or Europe** — wherever can respond the fastest.

Here's what the cross-region system does:
1. Your request hits Google's global load balancer IP address
2. The load balancer looks at your geographic location
3. It routes your request to the nearest healthy data center
4. If that data center is busy or has a problem, it silently reroutes to the next closest one
5. You get your search results in under a second — no matter where you are

This is only possible with a **cross-region** setup. A single-region load balancer could never achieve this globally low latency.

---

## Key Components of the Load Balancing Architecture in This Lab

### Virtual Machine Instances (Backends)

Four VMs are created across two regions (two per region). Each VM runs an **Apache web server** installed automatically via a **startup script**. These are the actual machines that serve web pages to users.

### Instance Groups

An **instance group** is a collection of VM instances treated as one unit. Instead of pointing the load balancer at individual VMs, you group them. This makes it easier to manage, scale, and monitor them.

- **Unmanaged instance group** — You manually add specific VMs to the group (used in this lab)
- **Managed instance group** — Google automatically creates and manages identical VMs for you

### Static External IP Address

The load balancer needs a **fixed, permanent IP address** that never changes — this is what users and DNS records point to. A static IP stays the same even if backend instances change.

### Health Check

A **health check** is like a nurse regularly checking on patients. It periodically sends a small request to each VM and checks if it gets a healthy response. If a VM stops responding, the load balancer marks it as **unhealthy** and stops sending traffic to it until it recovers.

Without health checks, the load balancer might send users to a broken server, causing errors.

### Named Ports

A **named port** maps a name (like `http`) to a port number (like `80`). This tells the load balancer which port on your instances to send HTTP traffic to. It's a label that makes configuration more readable and flexible.

### Backend Service

A **backend service** is the brain of the load balancer. It:
- Knows which instance groups exist
- Tracks the health of each instance via health checks
- Applies load balancing rules (e.g., CPU utilization limits)
- Decides where to send each request

You set rules like: *"If a server's CPU usage is above 80%, stop sending new requests to it and redirect them to another server."*

### Balancing Mode — CPU Utilization

In this lab, the balancing mode is set to **UTILIZATION** with a max CPU of **80%**. This means:
- If a backend VM is using less than 80% CPU → it can accept more traffic
- If it reaches 80% CPU → the load balancer shifts new requests to other VMs
- This prevents any VM from being overloaded while others sit idle

### URL Map

A **URL map** is a routing table for web requests. It looks at the URL of each incoming request and decides which backend service should handle it.

In this lab, there's only one backend service, so all traffic goes to the same place. But in production, you can route differently — for example: `example.com/images` → image servers, `example.com/api` → API servers.

### Target HTTP Proxy

The **target proxy** sits between the forwarding rule and the URL map. It receives the incoming request, looks at the URL map to decide where to send it, and forwards it to the correct backend service.

Think of it as a receptionist who reads the visitor's destination badge and sends them to the right department.

### Global Forwarding Rule

A **forwarding rule** is the entry point of the load balancer. It ties together:
- The **static IP address** (what users connect to)
- The **port** (80 for HTTP)
- The **target proxy** (where to forward the request)

When a user's request arrives at the load balancer's IP on port 80, the forwarding rule immediately hands it to the target proxy to process.

---

## Firewall Rules and Security

### Open Firewall (For Testing)

Initially, a firewall rule is created to allow HTTP traffic from **any source** (all IPs). This is only safe for testing — it lets you verify your instances are working correctly before locking things down.

### Restricted Firewall (For Production)

Once everything is confirmed working, the firewall is tightened to only allow traffic from:
- **Google's Load Balancer IP ranges** (`130.211.0.0/22`, `35.191.0.0/16`)
- **Google's Health Check IP ranges**

Now, users can only reach your VMs through the load balancer — they cannot directly access individual server IPs. This is much more secure.

### Bastion Host (Optional Security Step)

Once external IPs are removed from backend VMs, you can no longer SSH into them directly. A **bastion host** (also called a jump server) is one VM that keeps its external IP, acting as a secure entry point. You SSH into the bastion host first, then from there access the backend VMs through internal IPs.

---

## How Traffic Flows in This Lab

```
User (anywhere in the world)
        ↓
Global Forwarding Rule (Static IP, Port 80)
        ↓
Target HTTP Proxy
        ↓
URL Map (routes to default backend)
        ↓
Backend Service (checks health, applies CPU balancing)
        ↓
Instance Group — Region 1 (www-1, www-2)
        OR
Instance Group — Region 2 (www-3, www-4)
        ↓
Apache Web Server responds to user
```

The load balancer automatically picks the **closest healthy region** to the user. If Region 1 goes down, all traffic goes to Region 2 automatically.

---

## Key Terms at a Glance

| Term | What it means |
|---|---|
| HTTP Load Balancer | Distributes web traffic across servers in one region |
| Cross-Region Load Balancer | Distributes web traffic across servers in multiple regions globally |
| Instance | A virtual machine running a web server |
| Instance Group | A collection of VMs treated as one unit by the load balancer |
| Health Check | Periodic test to check if a VM is alive and healthy |
| Static IP | A permanent, fixed public IP address for the load balancer |
| Named Port | A label mapping a name (e.g., http) to a port number (e.g., 80) |
| Backend Service | Manages instance groups, health checks, and balancing rules |
| CPU Utilization Mode | Routes traffic based on how busy (CPU%) each server is |
| URL Map | Routes requests to different backends based on the URL path |
| Target HTTP Proxy | Receives requests and forwards them to the right backend via the URL map |
| Forwarding Rule | Entry point — connects the static IP, port, and target proxy |
| Startup Script | Commands that automatically run when a VM starts (e.g., install Apache) |
| Bastion Host | A secure intermediate VM used to access backend servers with no external IP |
| Firewall Rule | Controls which traffic is allowed to reach your VMs |
| Apache | A popular open-source web server installed on the backend VMs |