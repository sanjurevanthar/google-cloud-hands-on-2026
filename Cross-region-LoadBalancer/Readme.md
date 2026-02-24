# Cross-Region HTTP Load Balancing on Google Cloud

## Overview

This lab demonstrates how to build a **cross-region HTTP Load Balancer** on Google Cloud that distributes incoming web traffic across virtual machine instances in **two different geographic regions**. You configure the full load balancing stack — from VM instances and instance groups to health checks, backend services, URL maps, and global forwarding rules — and verify traffic is correctly distributed across regions.

## What You Will Do

- Create four VM instances (two per region) with Apache web servers installed via startup scripts
- Create a firewall rule to allow HTTP traffic for testing
- Reserve a global static IP address for the load balancer
- Create unmanaged instance groups and add VMs to them
- Configure a health check to monitor backend instance availability
- Build a backend service with CPU utilization-based balancing across both regions
- Create a URL map, target HTTP proxy, and global forwarding rule
- Test that traffic is distributed across both regions
- Lock down the firewall so only the load balancer can reach backend instances
- Optionally remove external IPs from backend VMs for maximum security

## Outcome

By the end of this lab, you will have:

- Four Apache web servers running across two GCP regions
- A globally accessible HTTP Load Balancer routing traffic to the nearest healthy region
- A secure firewall setup that blocks direct VM access and only allows load balancer traffic
- Practical experience configuring the full Google Cloud load balancing stack

## Where This Applies

- **Global web applications** — Serving users in multiple countries with low latency
- **High availability architecture** — Ensuring zero downtime even if an entire region fails
- **Scalable backend infrastructure** — Distributing load across multiple instances and regions
- **Enterprise-grade security** — Restricting backend VM access to load balancer traffic only

## Real-World Use Cases

- An e-commerce platform like **Flipkart or Amazon** running backend servers in multiple regions so that users in India, Europe, and the Americas all get fast responses from the nearest data center
- A **SaaS company** with a global customer base that needs its application to stay online even if one cloud region experiences an outage — cross-region load balancing automatically reroutes traffic to the surviving region
- A **media streaming platform** like YouTube that needs to serve video content requests from the nearest region to avoid buffering, with automatic failover if a regional server cluster goes down
- A **banking application** requiring high availability and security — backend servers have no public IPs and are only reachable through the load balancer, protecting them from direct internet attacks