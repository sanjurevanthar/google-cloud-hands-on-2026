# Notes.md

## What is Cloud Storage?

**Cloud Storage** is Google Cloud's object storage service. It lets you store files — images, videos, documents, backups — in the cloud and access them from anywhere. Files in Cloud Storage are stored in **buckets**, which are like folders in the cloud.

- A bucket has a **globally unique name** — no two buckets in all of Google Cloud can share the same name
- Files inside a bucket are called **objects**
- Buckets can be made **public**, meaning anyone on the internet can access the files inside
- You can choose the **location** of your bucket — a specific region, or multi-regional for wider availability

In this lab, Cloud Storage is used as the **backend** — the original source of the content being served to users.

---

## What is a CDN? (Content Delivery Network)

### The Problem CDN Solves

Imagine your Cloud Storage bucket is located in **Iowa, USA**. A user in **Tokyo, Japan** visits your website and requests an image stored in that bucket. The request has to travel all the way from Tokyo → Iowa → back to Tokyo. That round trip could take 1–2 seconds just because of physical distance. This delay is called **latency**.

Now multiply this by millions of users around the world all requesting the same image. Every single one of them is pulling data from Iowa. That's slow for users, and expensive for you.

A **CDN (Content Delivery Network)** solves this problem by storing **copies** of your content at **multiple locations around the world**, much closer to your users. These locations are called **edge locations** or **Points of Presence (PoPs)**.

---

### Real-World Example 1 — Amazon Prime Video

Think about how Amazon Prime Video works. When millions of people stream a popular TV show like *The Boys*, Amazon doesn't send the video files directly from one central server for every viewer worldwide. Instead, Amazon uses its CDN called **Amazon CloudFront**.

Here's what happens:
1. The video files are stored in Amazon S3 (their storage service) in one location
2. When a user in **London** first plays the show, the video is fetched from the origin server and simultaneously **cached** (saved) at an edge location near London
3. The next user in London who plays the same show gets it from the **London edge location** — not from the faraway origin
4. This makes the video load faster for everyone in that region

The result: less buffering, faster load times, and Amazon doesn't have to pay for the same data to travel across the world over and over again.

---

### Real-World Example 2 — Google Search Images

When you do a Google Image Search and click on an image, you might notice it loads almost instantly even if the image is originally stored on a server in another country. This is because Google uses **Cloud CDN** across its massive network of **over 100 edge locations** worldwide.

Here's what happens behind the scenes:
1. A popular image is stored on a web server or Cloud Storage bucket somewhere in the world
2. When a user in **Mumbai, India** requests that image for the first time, Google fetches it from the origin and caches a copy at an edge location **near Mumbai**
3. The next user in Mumbai (or anywhere in that region) who requests the same image gets it served from the **nearby cache** — in milliseconds
4. If nobody requests that image for a long time, the cache expires and the next request fetches a fresh copy from the origin again

The result: images load almost instantly no matter where in the world you are, because you're always getting data from a server that's physically close to you.

---

## How Cloud CDN Works — Step by Step

```
User Request
     ↓
Edge Location (CDN Cache)
     ↓ (Cache HIT)        ↓ (Cache MISS — first request)
Serve from cache       Fetch from backend (Cloud Storage)
                            ↓
                       Store in cache
                            ↓
                       Serve to user
```

- **Cache HIT** — The content is already stored at the edge location. It gets served immediately. Very fast.
- **Cache MISS** — The content is not yet at the edge location. It fetches from the backend (original storage), caches it there, and serves it. Slower — but only happens once per edge location.

Every subsequent request after the first one is a cache HIT — much faster and cheaper.

---

## What is TTL (Time to Live)?

**TTL** controls how long cached content stays in the edge location before it expires and needs to be refreshed from the backend.

- **Client TTL** — How long the user's browser keeps the file cached locally
- **Default TTL** — How long the CDN edge location caches the file if the backend doesn't specify otherwise
- **Maximum TTL** — The longest amount of time a file can be cached, even if the backend tries to set a longer time

In this lab, all TTL values are set to **1 minute** so you can quickly see the difference between a cache MISS (first request) and a cache HIT (subsequent requests).

In real production environments, static files like images and logos are often cached for **hours or days** since they don't change often.

---

## What is HTTP Load Balancing?

A **Load Balancer** is a service that sits in front of your backend and distributes incoming traffic across multiple servers or resources. It acts like a traffic controller — making sure no single server gets overwhelmed.

In Google Cloud, the **HTTP(S) Load Balancer** is a global service that:
- Accepts HTTP/HTTPS requests from users anywhere in the world
- Routes each request to the nearest or best available backend
- Integrates directly with Cloud CDN to serve cached content from edge locations

### What is an IP Address for a Load Balancer?

When you create a load balancer, it gets a **single public IP address**. Users around the world connect to this one address, and Google's network routes each request to the closest edge location automatically.

---

## Why Use a Load Balancer + CDN Together?

Using them together gives you the best of both worlds:

| Feature | Load Balancer | Cloud CDN |
|---|---|---|
| Distributes traffic | ✅ Across backends | ✅ Across edge locations |
| Reduces server load | ✅ | ✅ (cached content never hits backend) |
| Improves speed | Somewhat | Significantly |
| Global reach | ✅ | ✅ |

When Cloud CDN is enabled on a load balancer's backend bucket, cached content is served from the edge — it never even reaches the backend. This dramatically reduces cost and improves speed.

---

## What is a Cloud Storage Bucket Backend?

In this lab, the load balancer's **backend** is a Cloud Storage bucket — not a VM or a group of servers. This means:
- The load balancer serves **static content** (images, files, HTML) directly from Cloud Storage
- Cloud CDN caches that content at edge locations
- Users never directly access the bucket — they go through the load balancer's IP address
- This is a very common and cost-effective pattern for serving static websites, images, or file downloads at global scale

---

## Reading CDN Logs

Google Cloud **Logs Explorer** lets you inspect what happened with each request. Two key fields tell you whether content came from the cache or the backend:

| Log Field | Value | Meaning |
|---|---|---|
| `cacheLookup` | `true` | CDN checked the cache for this request |
| `cacheHit` | `true` | Content was found in the cache (served from edge) |
| `cacheHit` | missing/false | Content was NOT in cache (fetched from backend) |
| `statusDetails` | `response_from_cache` | Request was served from CDN cache |
| `statusDetails` | `response_sent_by_backend` | Request was served from origin (Cloud Storage) |

The first request always shows `response_sent_by_backend` — it fills the cache. Every request after that shows `response_from_cache` — it's much faster.

---

## Key Terms at a Glance

| Term | What it means |
|---|---|
| Cloud Storage | Google's file/object storage service |
| Bucket | A container for files in Cloud Storage |
| Object | A file stored inside a Cloud Storage bucket |
| Public Bucket | A bucket whose files anyone on the internet can access |
| CDN | Content Delivery Network — a global network of edge servers that cache content |
| Edge Location / PoP | A CDN server located close to end users around the world |
| Cache | A temporary storage copy of content at an edge location |
| Cache HIT | The requested content is already in the edge cache — served instantly |
| Cache MISS | The content is not in cache — fetched from origin and then cached |
| TTL (Time to Live) | How long cached content stays before it expires |
| Load Balancer | A service that distributes traffic and routes requests to backends |
| Backend Bucket | A Cloud Storage bucket used as the source of content for a load balancer |
| Cloud CDN | Google's CDN service integrated with its load balancer |
| LB IP Address | The single public IP address users connect to for the load balancer |
| Logs Explorer | Google Cloud's tool for viewing and searching request logs |