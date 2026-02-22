# commands.md

## Part 1 – Create the Node.js Application

Open the file in vi:
```bash
vi server.js
```

Press `i` to start editing, then add the following content:
```javascript
var http = require('http');
var handleRequest = function(request, response) {
  response.writeHead(200);
  response.end("Hello World!");
}
var www = http.createServer(handleRequest);
www.listen(8080);
```

Save and exit:
```
ESC  →  :wq  →  ENTER
```

Run the Node.js server to test it:
```bash
node server.js
```

Use the Cloud Shell **Web Preview on port 8080** to verify it works, then stop the server:
```
CTRL+C
```

---

## Part 2 – Create a Docker Container Image

Create the Dockerfile:
```bash
vi Dockerfile
```

Press `i` to start editing, then add:
```dockerfile
FROM node:6.9.2
EXPOSE 8080
COPY server.js .
CMD ["node", "server.js"]
```

Save and exit:
```
ESC  →  :wq  →  ENTER
```

Build the Docker image:
```bash
docker build -t hello-node:v1 .
```

Run the container locally to test it:
```bash
docker run -d -p 8080:8080 hello-node:v1
```

Test with curl:
```bash
curl http://localhost:8080
```

Find the running container ID:
```bash
docker ps
```

Stop the container:
```bash
docker stop [CONTAINER ID]
```

---

## Part 3 – Push the Image to Artifact Registry

Create a Docker repository in Artifact Registry:
```bash
gcloud artifacts repositories create my-docker-repo \
    --repository-format=docker \
    --location=YOUR_REGION \
    --project=YOUR_PROJECT_ID
```

Configure Docker authentication:
```bash
gcloud auth configure-docker
```

Tag the image with the registry path:
```bash
docker tag hello-node:v1 YOUR_REGION-docker.pkg.dev/YOUR_PROJECT_ID/my-docker-repo/hello-node:v1
```

Push the image to Artifact Registry:
```bash
docker push YOUR_REGION-docker.pkg.dev/YOUR_PROJECT_ID/my-docker-repo/hello-node:v1
```

---

## Part 4 – Create the Kubernetes Cluster

Set your project:
```bash
gcloud config set project PROJECT_ID
```

Create a 2-node cluster:
```bash
gcloud container clusters create hello-world \
    --num-nodes 2 \
    --machine-type e2-medium \
    --zone "ZONE"
```

---

## Part 5 – Create a Kubernetes Pod (Deployment)

Create the deployment:
```bash
kubectl create deployment hello-node \
    --image=YOUR_REGION-docker.pkg.dev/PROJECT_ID/my-docker-repo/hello-node:v1
```

View the deployment:
```bash
kubectl get deployments
```

View the pod:
```bash
kubectl get pods
```

Additional info and troubleshooting commands:
```bash
kubectl cluster-info
kubectl config view
kubectl get events
kubectl logs <pod-name>
```

---

## Part 6 – Expose the App to External Traffic

Expose the deployment with a LoadBalancer:
```bash
kubectl expose deployment hello-node --type="LoadBalancer" --port=8080
```

Get the external IP address:
```bash
kubectl get services
```

Visit the app in a browser:
```
http://<EXTERNAL_IP>:8080
```

---

## Part 7 – Scale Up the Service

Scale the deployment to 4 replicas:
```bash
kubectl scale deployment hello-node --replicas=4
```

Verify the deployment:
```bash
kubectl get deployment
```

List all running pods:
```bash
kubectl get pods
```

---

## Part 8 – Roll Out an Upgrade (v2)

Edit the server.js to update the response message:
```bash
vi server.js
```

Press `i` and update this line:
```javascript
response.end("Hello Kubernetes World!");
```

Save and exit:
```
ESC  →  :wq  →  ENTER
```

Build the new image version:
```bash
docker build -t hello-node:v2 .
```

Tag the new image:
```bash
docker tag hello-node:v2 YOUR_REGION-docker.pkg.dev/YOUR_PROJECT_ID/my-docker-repo/hello-node:v2
```

Push the new image:
```bash
docker push YOUR_REGION-docker.pkg.dev/YOUR_PROJECT_ID/my-docker-repo/hello-node:v2
```

Edit the deployment to update the image version:
```bash
kubectl edit deployment hello-node
```

Find this line under `spec.template.spec.containers.image` and change `v1` to `v2`:
```
image: YOUR_REGION-docker.pkg.dev/PROJECT_ID/my-docker-repo/hello-node:v1
```

Save and exit:
```
ESC  →  :wq  →  ENTER
```

Verify the rollout:
```bash
kubectl get deployments
```