# commands.md

## Part 1 – Set Up the Kubernetes Cluster

Set the compute zone:
```bash
gcloud config set compute/zone Zone
```

Create the Kubernetes cluster:
```bash
gcloud container clusters create io --zone Zone
```

Re-authenticate to the cluster if Cloud Shell disconnects:
```bash
gcloud container clusters get-credentials io
```

---

## Part 2 – Get the Sample Code

Copy the sample code from Cloud Storage:
```bash
gcloud storage cp -r gs://spls/gsp021/* .
```

Change into the working directory:
```bash
cd orchestrate-with-kubernetes/kubernetes
```

List the available files:
```bash
ls
```

---

## Part 3 – Quick Kubernetes Demo (nginx)

Deploy a single nginx container:
```bash
kubectl create deployment nginx --image=nginx:1.27.0
```

View the running pod:
```bash
kubectl get pods
```

Expose the deployment as a LoadBalancer:
```bash
kubectl expose deployment nginx --port 80 --type LoadBalancer
```

List services and get the external IP:
```bash
kubectl get services
```

Test the nginx container:
```bash
curl http://<External IP>:80
```

---

## Part 4 – Create a Pod Using a YAML Manifest

View the Pod configuration file:
```bash
cat pods/fortune-app.yaml
```

Create the Pod:
```bash
kubectl create -f pods/fortune-app.yaml
```

List all running pods:
```bash
kubectl get pods
```

Get detailed info about the Pod:
```bash
kubectl describe pods fortune-app
```

---

## Part 5 – Interact with the Pod

**In Terminal 2** — Set up port-forwarding:
```bash
kubectl port-forward fortune-app 10080:8080
```

**In Terminal 1** — Test the Pod with curl:
```bash
curl http://127.0.0.1:10080
```

Hit the secure endpoint (expects auth):
```bash
curl http://127.0.0.1:10080/secure
```

Login and get a JWT token:
```bash
curl -u user http://127.0.0.1:10080/login
```

Store the token in an environment variable:
```bash
TOKEN=$(curl -u user http://127.0.0.1:10080/login | jq -r '.token')
```

Access the secure endpoint with the token:
```bash
curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure
```

View Pod logs:
```bash
kubectl logs fortune-app
```

**In Terminal 3** — Stream logs in real-time:
```bash
kubectl logs -f fortune-app
```

Open an interactive shell inside the Pod:
```bash
kubectl exec fortune-app --stdin --tty -c fortune-app -- /bin/sh
```

Test network connectivity from inside the container:
```bash
ping -c 3 google.com
```

Exit the shell:
```bash
exit
```

---

## Part 6 – Create a Secure Pod and NodePort Service

Make sure you are in the correct directory:
```bash
cd ~/orchestrate-with-kubernetes/kubernetes
```

View the secure pod configuration:
```bash
cat pods/secure-fortune.yaml
```

Create the TLS secret, nginx config, and secure pod:
```bash
kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
kubectl create -f pods/secure-fortune.yaml
```

View the service configuration:
```bash
cat services/fortune-app.yaml
```

Create the fortune-app service:
```bash
kubectl create -f services/fortune-app.yaml
```

Allow traffic to the NodePort:
```bash
gcloud compute firewall-rules create allow-fortune-nodeport --allow tcp:31000
```

Get an external IP of a node:
```bash
gcloud compute instances list
```

Test the secure service:
```bash
curl -k https://<EXTERNAL_IP>:31000
```

---

## Part 7 – Debug and Fix Labels

Check which pods have the `app=fortune-app` label:
```bash
kubectl get pods -l "app=fortune-app"
```

Check which pods have BOTH required labels:
```bash
kubectl get pods -l "app=fortune-app,secure=enabled"
```

Add the missing label to the `secure-fortune` pod:
```bash
kubectl label pods secure-fortune 'secure=enabled'
```

Verify labels were updated:
```bash
kubectl get pods secure-fortune --show-labels
```

Verify the service now has endpoints:
```bash
kubectl describe services fortune-app | grep Endpoints
```

Test the service again:
```bash
curl -k https://<EXTERNAL_IP>:31000
```

---

## Part 8 – Create Microservice Deployments

View the auth deployment config:
```bash
cat deployments/auth.yaml
```

Create the auth deployment and service:
```bash
kubectl create -f deployments/auth.yaml
kubectl create -f services/auth.yaml
```

Create the fortune deployment and service:
```bash
kubectl create -f deployments/fortune-service.yaml
kubectl create -f services/fortune-service.yaml
```

Create the frontend configmap, deployment, and service:
```bash
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
```

Get the frontend external IP:
```bash
kubectl get services frontend
```

Test the full microservices app:
```bash
curl -k https://<EXTERNAL-IP>
```