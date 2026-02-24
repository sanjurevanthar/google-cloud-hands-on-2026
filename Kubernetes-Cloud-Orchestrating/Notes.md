# Notes.md

## How This Lab Differs from the Previous Kubernetes Lab

This is one of the most important things to understand before diving in. You have already done a Kubernetes lab (Hello Node Kubernetes — GSP005). Both labs use Kubernetes and GKE, but they teach very different things. Here's a clear comparison:

| Topic | Previous Lab (GSP005 — Hello Node) | This Lab (GSP021 — Orchestrate with Kubernetes) |
|---|---|---|
| App type | One simple Node.js app | A multi-service app broken into 3 microservices |
| Focus | Basic Kubernetes setup and workflow | Deep dive into Pods, Services, Deployments, Labels |
| How app is created | `kubectl create deployment` (one command) | YAML manifest files for full control |
| Services | One LoadBalancer service | ClusterIP, NodePort, and LoadBalancer services |
| Architecture | Single monolith container | auth + fortune + frontend (microservices) |
| Deployment style | Quick demo-style | Production-style with configuration files |
| Labels | Not covered | Core concept — used to connect pods to services |
| Port-forwarding | Not covered | Used to test pods before exposing them |
| Secrets & ConfigMaps | Not covered | Used to manage TLS certs and nginx config |
| Goal | Get something running fast | Understand how production Kubernetes works |

**In short:** The previous lab showed you the *what* of Kubernetes. This lab shows you the *how* and *why* — how real production applications are structured and managed in Kubernetes.

---

## What is Kubernetes Orchestration?

The word **orchestration** comes from music — an orchestra has many musicians playing different instruments, and a conductor coordinates them so everything sounds right together. In software, **container orchestration** means coordinating many containers (each doing a specific job) so they work together as one system.

Kubernetes is the most popular container orchestration tool. It handles:
- Starting and stopping containers automatically
- Restarting containers if they crash
- Distributing containers across many machines
- Connecting containers to each other through services
- Scaling containers up or down based on demand

---

## What Are Microservices?

In the **previous lab**, you built a single app — one container that did everything. This is called a **monolith**. In this lab, you break that idea apart into **microservices**.

A **microservice** is a small, focused piece of an application that does just one thing. Instead of one big app that handles login, content, and routing all together, you split it into three separate services:

- **auth** — Handles login and generates security tokens (JWT)
- **fortune** — Serves the actual fortune content to authenticated users
- **frontend** — Acts as the gateway — routes user requests to auth or fortune depending on what's needed

Think of it like a restaurant:
- The **host** (frontend) greets you and directs you
- The **waiter** (auth) checks your reservation and seat you
- The **kitchen** (fortune) prepares and delivers your food

Each role is separate. If the kitchen gets busy, you hire more cooks — you don't hire more hosts. This is the power of microservices — each part can be **scaled independently**.

---

## Pods — A Deeper Look

You learned about Pods in the previous lab, but this lab goes deeper.

A **Pod** is the smallest deployable unit in Kubernetes. It wraps one or more containers that need to work closely together.

### What's Inside a Pod?

- **Containers** — The actual running application(s)
- **Volumes** — Shared storage that containers in the pod can access. Volumes live as long as the pod lives.
- **One shared IP address** — All containers in a pod share the same network, so they talk to each other via `localhost`
- **Shared namespace** — Containers in the same pod can see each other's processes and network

### Pod Configuration Files (YAML)

In the previous lab, pods were created with quick `kubectl` commands. In this lab, pods are described in **YAML manifest files**. YAML files give you full control and are the standard way to manage Kubernetes resources in production.

A basic Pod YAML looks like this:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: fortune-app
  labels:
    app: fortune-app
spec:
  containers:
    - name: fortune-app
      image: "path/to/image:1.0.0"
      ports:
        - containerPort: 8080
      resources:
        limits:
          cpu: 0.2
          memory: "20Mi"
```

Key sections:
- `metadata.labels` — Tags attached to the pod (used by services to find pods)
- `spec.containers` — What containers to run
- `resources.limits` — CPU and memory caps to prevent one pod from using too much

---

## Labels — The Glue That Connects Everything

Labels are one of the most important concepts in this lab and something the previous lab did not cover.

A **label** is a simple key-value tag attached to a Kubernetes resource. For example:
```
app: fortune-app
secure: enabled
```

Labels are how **Services find Pods**. A service doesn't point to specific pod names — instead it uses a **label selector** to say: *"send traffic to any pod that has these labels."*

This is powerful because:
- Pods can come and go (they restart, get replaced)
- As long as a new pod has the right labels, the service automatically picks it up
- You can add or remove labels at any time with `kubectl label`

### Why Labels Matter — The Bug in This Lab

In Task 8, the `fortune-app` service has zero endpoints (no pods sending it traffic) even though pods are running. The reason: the service requires pods to have **both** `app=fortune-app` AND `secure=enabled`. The pods only have `app=fortune-app`. Adding the missing label with `kubectl label pods secure-fortune 'secure=enabled'` immediately fixes the connection.

This teaches a critical real-world lesson: always check labels when a service isn't routing traffic to pods.

---

## Services — Three Types Explained

In the previous lab, you used only one type of service (LoadBalancer). This lab introduces all three types:

### 1. ClusterIP (Internal Only)
- The **default** service type
- The service is only reachable **inside the cluster** — not from the internet
- Used for services that only other services inside the cluster need to talk to
- Example in this lab: the `auth` and `fortune` services. Only the frontend needs to talk to them — users never access them directly.

### 2. NodePort
- Exposes the service on a **specific port on every node** in the cluster
- Accessible from outside the cluster using `<NodeExternalIP>:<NodePort>`
- The nodePort number must be between 30000–32767
- Example in this lab: the `secure-fortune` service uses NodePort 31000

### 3. LoadBalancer
- Creates a **cloud load balancer** with a public external IP
- The easiest way to expose a service to the internet
- Used in this lab for the **frontend** service
- Example in the previous lab: `kubectl expose deployment nginx --type LoadBalancer`

---

## Port Forwarding — Testing Without Exposing

In the previous lab, everything was immediately exposed to the internet. This lab introduces **port-forwarding** for safer testing.

`kubectl port-forward fortune-app 10080:8080` creates a tunnel:
```
Your machine (port 10080) → Pod (port 8080)
```

This lets you test a pod **privately from your terminal** without creating a public IP or service. It's like opening a private door into the pod just for testing. Once you're confident everything works, you then expose it properly.

---

## Deployments — Managing Pods at Scale

A **Deployment** is a step above a raw Pod. It's a controller that manages pods for you.

### What Deployments Do That Raw Pods Don't

| Situation | Raw Pod | Deployment |
|---|---|---|
| Node goes down | Pod is gone permanently | Deployment creates a replacement automatically |
| You want 4 copies | Create 4 pods manually | Set `replicas: 4` — Deployment handles the rest |
| You want to update | Delete and recreate manually | Rolling update with zero downtime |
| Pod crashes | Stays dead | Auto-restarted |

### Behind the Scenes: ReplicaSets

When you create a Deployment, it automatically creates a **ReplicaSet** behind the scenes. The ReplicaSet is what actually ensures the correct number of pod copies are running at all times. You usually don't interact with ReplicaSets directly — the Deployment manages them.

### Deployment YAML Structure

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth
spec:
  replicas: 1               ← How many copies of the pod to run
  selector:
    matchLabels:
      app: auth             ← Which pods this deployment manages
  template:
    metadata:
      labels:
        app: auth
    spec:
      containers:
        - name: auth
          image: "path/to/auth:1.0.0"
```

---

## Secrets and ConfigMaps

These are two new concepts in this lab not covered previously:

### Secrets
A **Secret** stores sensitive data like passwords, API keys, and TLS certificates. Kubernetes keeps secrets encoded and separate from your code.

In this lab:
```bash
kubectl create secret generic tls-certs --from-file tls/
```
This stores TLS certificate files as a secret so the nginx container can use them for HTTPS without hardcoding them in the image.

### ConfigMaps
A **ConfigMap** stores non-sensitive configuration data. Instead of baking configuration into your container image, you store it in Kubernetes and inject it at runtime.

In this lab:
```bash
kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
```
This stores the nginx configuration file so it can be mounted into the container.

Using secrets and ConfigMaps means you can run the same container image in dev, staging, and production — just swap out the configs.

---

## JWT Tokens and Authentication

This lab introduces a simple authentication flow:

1. User logs in with a username and password → gets a **JWT token** (a signed string that proves identity)
2. User sends that token in the `Authorization` header for future requests
3. The app verifies the token before serving content

This is the same auth pattern used by APIs in the real world (like Google, GitHub, etc.). The `auth` microservice in this lab handles all of this — which is why it's a separate service: authentication logic is isolated and can be updated independently.

---

## How the Three Microservices Connect

```
User (internet)
      ↓
  frontend service (LoadBalancer — public IP)
      ↓
  frontend pod (nginx — routes internally)
      ↓
  ┌──────────────────────┐
  │                      │
auth service          fortune service
(ClusterIP)           (ClusterIP)
  │                      │
auth pod              fortune pod
(generates JWT)       (serves fortunes)
```

- The user only ever talks to the **frontend**
- The frontend talks to **auth** for login and **fortune** for content
- auth and fortune are internal — the user never reaches them directly

---

## Key Terms at a Glance

| Term | What it means |
|---|---|
| Orchestration | Coordinating many containers to work together as a system |
| Monolith | A single app that handles all functionality in one codebase |
| Microservice | A small, independent service that does one specific job |
| Pod | The smallest unit in Kubernetes — wraps one or more containers |
| YAML Manifest | A configuration file that describes a Kubernetes resource |
| Label | A key-value tag on a resource used to identify and select it |
| Label Selector | A filter used by services to find pods with matching labels |
| Volume | Shared storage inside a Pod, available to all containers in it |
| Service | A stable endpoint that routes traffic to matching pods |
| ClusterIP | Internal-only service — reachable only inside the cluster |
| NodePort | Exposes a service on a port on every node in the cluster |
| LoadBalancer | Creates a public IP and cloud load balancer for external access |
| Port Forwarding | A tunnel from your machine to a pod port for private testing |
| Deployment | Manages pod replicas, updates, and restarts automatically |
| ReplicaSet | Created by deployments to ensure the right number of pods run |
| Secret | Kubernetes resource for storing sensitive data (certs, passwords) |
| ConfigMap | Kubernetes resource for storing non-sensitive configuration data |
| JWT Token | A signed token used to prove a user's identity in API requests |
| kubectl exec | Command to run a shell or command inside a running container |
| kubectl logs | Command to view output logs from a running pod |
| kubectl label | Command to add or update labels on a Kubernetes resource |
| kubectl describe | Command to get detailed info about a Kubernetes resource |