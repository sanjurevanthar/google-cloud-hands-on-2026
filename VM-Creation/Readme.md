# Getting Started with Google Compute Engine

## Overview

This lab walks you through creating and managing Virtual Machine (VM) instances on **Google Compute Engine (GCE)** — Google Cloud's core infrastructure service. You will use two different methods to create VMs and deploy a live web server accessible from the internet.

## What You Will Do

- Create a VM using the **Google Cloud Console** (GUI)
- Install and run an **NGINX web server** on the VM
- Create a second VM using the **gcloud command-line tool**
- Connect to VMs via **SSH**

## Outcome

By the end of this lab, you will have:

- Two running VM instances (`gcelab` and `gcelab2`)
- A publicly accessible web server running on `gcelab`
- Hands-on experience with both GUI and CLI-based VM management on GCP

## Where This Applies

- Hosting websites or web applications on cloud infrastructure
- Setting up development or testing environments
- Running backend services that need full operating system access
- Learning the foundations of cloud computing before moving to advanced services like Kubernetes or App Engine

## Real-World Use Cases

- A startup hosting its first web application on a cloud VM
- A developer spinning up a temporary environment to test a deployment
- A company migrating on-premise servers to the cloud
- An engineer automating infrastructure creation with gcloud scripts