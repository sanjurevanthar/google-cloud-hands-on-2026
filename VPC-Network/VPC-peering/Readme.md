# VPC Network Peering Across Projects in Google Cloud

## Overview

This lab walks you through setting up **VPC Network Peering** between two separate Google Cloud projects. Each project gets its own custom VPC network with a subnet and a VM instance. You then establish a private peering connection between the two networks and verify connectivity using the `ping` command.

## What You Will Do

- Create a custom VPC network and subnet in **Project A**
- Create a custom VPC network and subnet in **Project B**
- Deploy a VM instance in each project's network
- Configure firewall rules to allow SSH and ICMP traffic
- Set up a two-sided VPC peering connection between the networks
- Verify private connectivity between VMs across projects using `ping`

## Outcome

By the end of this lab, you will have:

- Two VPC networks in separate projects connected via private peering
- VMs in both projects able to communicate using internal IP addresses
- Active peering routes automatically exchanged between both networks
- No public internet required for cross-project communication

## Where This Applies

- **Multi-project enterprise architectures** — Connecting isolated team or department networks privately
- **SaaS platforms** — Offering private service access to customer networks
- **Hybrid and shared service models** — Centralizing shared services (e.g., logging, security) while keeping workloads in separate projects
- **Post-merger IT integration** — Connecting networks across different organizations without restructuring

## Real-World Use Cases

- A company with separate GCP projects for dev, staging, and production that needs these environments to communicate privately
- A managed service provider connecting its infrastructure network to a client's VPC for private service delivery
- Two companies that have merged and need to connect their existing GCP environments without exposing traffic to the internet
- A central security or monitoring team that needs private access to workloads running across multiple project networks