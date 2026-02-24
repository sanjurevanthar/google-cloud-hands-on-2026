# Orchestrating Containers with Kubernetes Engine

## Overview

This lab builds on basic Kubernetes knowledge to explore how real production applications are structured and managed. You provision a Kubernetes cluster on GKE, work with Pod manifests, learn how Services use labels to route traffic, and break a monolithic application into three independent **microservices** — each deployed and managed separately using Kubernetes Deployments.

## How This Differs from the Previous Kubernetes Lab

The previous lab (Hello Node Kubernetes) showed you the basics — deploy one container, expose it, scale it. This lab goes much deeper:

- Apps are defined using **YAML manifest files** instead of single `kubectl` commands
- You work with all **three service types** — ClusterIP, NodePort, and LoadBalancer
- You learn how **labels connect services to pods** and debug when they don't match
- You use **Secrets and ConfigMaps** for managing TLS certificates and configuration
- You break an app into **three microservices** (auth, fortune, frontend) with independent deployments
- You use **port-forwarding** to test pods privately before exposing them

## What You Will Do

- Provision a GKE Kubernetes cluster and get sample code
- Run a quick nginx demo using `kubectl create` and `kubectl expose`
- Create a Pod from a YAML manifest file and interact with it via port-forwarding, curl, logs, and exec
- Create a NodePort service, debug a label mismatch, and fix it with `kubectl label`
- Create Secrets and ConfigMaps for TLS and nginx configuration
- Deploy three separate microservices (auth, fortune, frontend) using Deployments and Services
- Access the complete multi-service application through a public LoadBalancer IP

## Outcome

By the end of this lab, you will have:

- A fully running 3-microservice application on Kubernetes
- Hands-on experience with YAML manifests, label selectors, and all three service types
- Practical debugging skills for label mismatches and endpoint issues
- An understanding of how Deployments manage pod lifecycle automatically

## Where This Applies

- **Production Kubernetes deployments** — The YAML-based workflow used here is the industry standard
- **Microservices architecture** — Breaking monolithic apps into independent, scalable services
- **DevOps and platform engineering** — Managing containerized workloads with full operational control
- **Cloud-native application development** — Building resilient, self-healing distributed systems

## Real-World Use Cases

- A fintech company running separate microservices for authentication, payment processing, and user dashboards — each deployed and scaled independently on Kubernetes
- An e-commerce platform breaking its monolith into catalog, cart, order, and auth services so each team can deploy their service without affecting others
- A SaaS company managing multiple customer-facing APIs as Kubernetes Deployments, using ClusterIP for internal service communication and LoadBalancer for public endpoints
- A startup using Secrets and ConfigMaps to manage environment-specific configurations across dev, staging, and production clusters without changing their container images