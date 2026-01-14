# Managing Deployments Using Kubernetes Engine

This project demonstrates how Kubernetes deployments can be used to manage application lifecycles such as scaling, rolling updates, canary releases, and blue-green switchovers. The exercise was performed on Google Kubernetes Engine (GKE) using the `kubectl` CLI.

## Objectives

Through this exercise, the following deployment capabilities were practiced:

- Creating Kubernetes deployments from manifest files
- Scaling deployments horizontally
- Performing rolling updates
- Pausing and resuming rollouts
- Performing rollback actions
- Executing canary release patterns
- Performing blue-green deployment transitions

These are common DevOps deployment patterns that enable safe production rollouts and controlled application releases.

---

## Deployment Concepts Summary

**Deployment object**  
Defines the desired state of application replicas, including version, container image, and labels.

**ReplicaSet**  
Created and managed automatically by a Deployment to maintain the configured number of Pods.

**Rolling Update**  
Gradually replaces old Pods with new Pods without downtime.

**Rollback**  
Reverts to a previously working ReplicaSet if a release is defective.

**Blue-Green Deployment**  
Two parallel environments ("blue" and "green") exist; traffic is switched at once.

**Canary Deployment**  
A small percentage of traffic is routed to a new version to validate before full release.

---

## Tools Used

- Google Kubernetes Engine (GKE)
- `kubectl` CLI
- Google Cloud CLI (`gcloud`)
- Container images hosted in Artifact Registry

---

## Cluster Topology (Logical View)

Single GKE cluster with:

- 3 worker nodes
- 1 Deployment for "blue" release
- 1 Service exposing the app externally
- Optional secondary Deployment for canary or green release

---

## Learning Outcomes

By the end of this exercise, the following capabilities were validated:

- Container orchestration fundamentals
- Declarative deployment management
- Zero-downtime update strategies
- Controlled rollout & rollback flows
- Kubernetes service-selector based routing

This aligns with practical real-world DevOps CI/CD workflows.
