# Notes.md

## What is a Container?

Think of a container like a lunchbox. It holds everything a meal needs — food, utensils, napkins — all packed together. In software, a container holds everything an application needs to run: the code, the libraries, the settings, and the runtime. You can pick up that container and run it on any computer that supports containers, and it will behave exactly the same way every time.

This solves a very common problem in software development: *"It works on my machine but not on yours."* With containers, the environment travels with the app.

Containers are:
- **Lightweight** — They share the host machine's operating system, so they use fewer resources than full virtual machines
- **Fast** — They start up in seconds
- **Portable** — Run the same container on a laptop, a server, or in the cloud
- **Isolated** — Each container runs independently without interfering with others

---

## What is Docker?

Docker is the most popular tool for creating and running containers. It gives you a standard way to package your application into a container image and then run it anywhere.

Think of Docker in two parts:

### Docker Image
A Docker image is like a recipe or a blueprint. It contains all the instructions needed to build your container — which operating system to use, which files to include, which commands to run. Images are built once and can be shared.

### Docker Container
A Docker container is a running instance of an image. Just like you can bake multiple cakes from one recipe, you can run multiple containers from one image.

### Dockerfile
A Dockerfile is a plain text file where you write the instructions Docker uses to build your image. For example:

```
FROM node:6.9.2       ← Start from an existing Node.js image
EXPOSE 8080           ← Tell Docker the app uses port 8080
COPY server.js .      ← Copy your app code into the image
CMD ["node","server.js"] ← Command to run when the container starts
```

Each line adds a layer to the image. Docker reuses unchanged layers when rebuilding, which makes updates faster.

### Key Docker Commands Summary

| Action | What it does |
|---|---|
| `docker build` | Creates an image from a Dockerfile |
| `docker run` | Starts a container from an image |
| `docker ps` | Lists running containers |
| `docker stop` | Stops a running container |
| `docker push` | Uploads an image to a registry |
| `docker tag` | Gives an image a name/label |

---

## What is a Container Registry?

Once you build a container image, you need somewhere to store and share it. A **container registry** is like a library for Docker images. You push (upload) your image to the registry, and any machine or server can pull (download) it from there.

**Google Artifact Registry** is Google Cloud's private container registry. It stores your Docker images securely within your GCP project, so only your authorized services (like Kubernetes) can access them.

---

## What is Kubernetes?

Kubernetes (often shortened to **K8s**) is an open-source system that automatically manages containers at scale. If Docker helps you run one container, Kubernetes helps you run, manage, and coordinate hundreds or thousands of containers across many machines.

Think of it like this: if containers are shipping boxes, Docker is the truck that moves one box, and Kubernetes is the entire logistics company managing fleets of trucks, warehouses, and delivery routes.

### What Kubernetes Does For You

- **Runs your containers** across multiple machines (nodes)
- **Restarts containers** if they crash
- **Scales up or down** the number of running containers based on demand
- **Distributes traffic** across containers using load balancing
- **Rolls out updates** to new versions without downtime
- **Self-heals** — if a node (machine) goes down, Kubernetes moves containers to healthy nodes

---

## Key Kubernetes Concepts

### Cluster
A Kubernetes **cluster** is the group of machines (called nodes) that Kubernetes manages. It has two main parts:

- **Control Plane (Master)** — The brain of the cluster. It makes decisions about scheduling, scaling, and health. In Google Kubernetes Engine (GKE), Google manages this for you.
- **Worker Nodes** — The machines that actually run your containers. In GKE, these are Compute Engine VMs.

### Pod
A **pod** is the smallest unit in Kubernetes. It is a wrapper around one or more containers that should run together. Pods share the same network and storage within the pod.

- Most pods contain just one container
- All containers in a pod share the same IP address and can communicate with each other directly
- If a pod dies, Kubernetes automatically creates a new one

### Deployment
A **deployment** tells Kubernetes how many copies (replicas) of a pod to run and which container image to use. It also handles updates and rollbacks.

- You don't manage pods directly — you manage deployments
- The deployment watches your pods and makes sure the right number are always running
- If you want 4 copies of your app running, you set `replicas: 4` in the deployment

### Service
A **service** is how you expose your application to the outside world (or to other parts of the cluster). Since pods can be created and destroyed at any time, their IP addresses change. A service gives you a stable address that always routes to the right pods.

- **ClusterIP** — Only accessible inside the cluster (default)
- **LoadBalancer** — Creates a public IP and routes external internet traffic to your pods. This is what's used in this lab to make the app accessible from a browser.

### ReplicaSet
A **ReplicaSet** is what actually enforces the number of pod replicas you specified in a deployment. The deployment creates and manages the ReplicaSet automatically. You usually don't interact with ReplicaSets directly.

### kubectl
**kubectl** is the command-line tool used to talk to your Kubernetes cluster. You use it to create, view, update, and delete Kubernetes resources.

| Command | What it does |
|---|---|
| `kubectl get pods` | Lists all running pods |
| `kubectl get deployments` | Lists all deployments |
| `kubectl get services` | Lists all services and their IP addresses |
| `kubectl create deployment` | Creates a new deployment |
| `kubectl expose deployment` | Creates a service to expose a deployment |
| `kubectl scale deployment` | Changes the number of pod replicas |
| `kubectl edit deployment` | Opens deployment config in a text editor |
| `kubectl logs <pod>` | Shows logs from a pod |
| `kubectl cluster-info` | Shows info about the cluster |
| `kubectl get events` | Lists recent cluster events for troubleshooting |

---

## What is Google Kubernetes Engine (GKE)?

**Google Kubernetes Engine** is Google Cloud's fully managed Kubernetes service. Instead of setting up and maintaining your own Kubernetes cluster from scratch, GKE handles the hard parts for you:

- The control plane (master) is fully managed and updated by Google
- Worker nodes are Compute Engine VMs that GKE provisions for you
- Integrates automatically with other GCP services like Artifact Registry, Cloud Load Balancing, and Cloud Monitoring

This means you can focus on deploying and running your apps rather than managing infrastructure.

---

## What is Node.js?

**Node.js** is a runtime that lets you run JavaScript code on a server (outside of a web browser). In this lab, a simple Node.js web server is built that listens on port 8080 and responds with "Hello World!" to any request. This app is then packaged into a Docker container and deployed to Kubernetes.

---

## How It All Fits Together

Here is the full picture of what happens in this lab, step by step:

```
1. Write a Node.js app  →  runs on port 8080
2. Package it into a Docker image  →  using a Dockerfile
3. Push the image to Artifact Registry  →  so Kubernetes can access it
4. Create a GKE cluster  →  group of VMs managed by Kubernetes
5. Create a Deployment  →  tells Kubernetes to run the container
6. Expose it as a Service  →  gives it a public IP via a Load Balancer
7. Scale it up  →  run 4 copies of the pod
8. Roll out an update  →  push v2 image, Kubernetes updates with zero downtime
```

---

## Rolling Updates — Zero Downtime Deployments

One of Kubernetes' most powerful features is **rolling updates**. When you update your app to a new version, Kubernetes does not shut everything down and restart. Instead, it:

1. Starts new pods with the new version
2. Waits until they are healthy
3. Gradually shuts down old pods

At no point are all pods down at the same time, so users never experience an outage. You can control how many pods are replaced at once using `maxSurge` and `maxUnavailable` settings.

---

## Scaling in Kubernetes

Kubernetes makes scaling extremely simple. Instead of manually starting or stopping instances, you declare the desired state:

```bash
kubectl scale deployment hello-node --replicas=4
```

Kubernetes then makes sure exactly 4 pod replicas are always running. If one crashes, it starts a new one automatically. This **declarative approach** means you describe what you want, and Kubernetes figures out how to make it happen.

---

## Key Terms at a Glance

| Term | What it means |
|---|---|
| Container | A packaged, portable unit that includes an app and everything it needs to run |
| Docker | The tool used to build and run containers |
| Dockerfile | A recipe file that tells Docker how to build a container image |
| Image | A built, ready-to-run snapshot of a container |
| Registry | A storage service for container images (e.g., Artifact Registry) |
| Kubernetes (K8s) | A system that manages and orchestrates containers at scale |
| GKE | Google's managed Kubernetes service |
| Cluster | A group of machines managed by Kubernetes |
| Node | A single machine (VM) in a Kubernetes cluster |
| Pod | The smallest unit in Kubernetes; wraps one or more containers |
| Deployment | Manages how many pod replicas run and handles updates |
| Service | A stable network endpoint that exposes pods to traffic |
| LoadBalancer | A type of service that gives pods a public IP address |
| kubectl | The CLI tool to interact with a Kubernetes cluster |
| Rolling Update | A way to deploy new app versions gradually without downtime |
| Replica | A copy of a running pod |
| Node.js | A JavaScript runtime used to build the sample web server in this lab |