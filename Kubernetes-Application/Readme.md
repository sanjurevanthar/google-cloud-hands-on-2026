# Hello Node Kubernetes — Deploying a Containerized App on GKE

## Overview

This lab walks you through building a simple **Node.js web application**, packaging it into a **Docker container**, and deploying it to a **Google Kubernetes Engine (GKE)** cluster. It covers the full lifecycle of a containerized app — from writing code to scaling it in production.

## What You Will Do

- Write a simple Node.js "Hello World" web server
- Build and test a Docker container image locally
- Push the image to **Google Artifact Registry**
- Create a Kubernetes cluster on **Google Kubernetes Engine**
- Deploy the app as a Kubernetes **pod** using a **Deployment**
- Expose the app to the internet using a Kubernetes **LoadBalancer Service**
- Scale the app from 1 pod to 4 replicas
- Roll out a new version of the app with zero downtime

## Outcome

By the end of this lab, you will have:

- A containerized Node.js app running on a 2-node Kubernetes cluster
- A publicly accessible URL serving your application
- 4 running pod replicas load-balanced across the cluster
- A v2 deployment rolled out without any service interruption

## Where This Applies

- **Cloud-native application development** — Building and running apps in containers
- **DevOps and CI/CD pipelines** — Automating build, push, and deploy workflows
- **Microservices architecture** — Running independently deployable services at scale
- **Site Reliability Engineering (SRE)** — Using Kubernetes for self-healing and auto-scaling

## Real-World Use Cases

- A startup deploying its backend API to Kubernetes for automatic scaling during traffic spikes
- A team rolling out weekly app updates using zero-downtime rolling deployments
- An enterprise running dozens of microservices across a managed Kubernetes cluster
- A developer packaging a legacy app into containers to move it to the cloud without rewriting it