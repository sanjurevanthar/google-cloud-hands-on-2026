# Commands Executed

## Cluster Setup

gcloud config set compute/zone <ZONE>
gcloud container clusters create bootcamp
--machine-type e2-small
--num-nodes 3
--scopes "storage-rw"

## Inspect Deployment Object

kubectl explain deployment
kubectl explain deployment --recursive
kubectl explain deployment.metadata.name

## Create Deployment

kubectl create -f deployments/fortune-app-blue.yaml
kubectl get deployments
kubectl get replicasets
kubectl get pods

## Create Service

kubectl create -f services/fortune-app.yaml
kubectl get services fortune-app

## Check Service Output

curl http://$(kubectl get svc fortune-app -o=jsonpath="{.status.loadBalancer.ingress[0].ip}")/version

## Scaling

kubectl scale deployment fortune-app-blue --replicas=5
kubectl scale deployment fortune-app-blue --replicas=3

## Rolling Update

kubectl edit deployment fortune-app-blue
kubectl get replicaset
kubectl rollout history deployment/fortune-app-blue
kubectl rollout pause deployment/fortune-app-blue
kubectl rollout resume deployment/fortune-app-blue

## Rollback

kubectl rollout undo deployment/fortune-app-blue

## Canary Deployment

kubectl create -f deployments/fortune-app-canary.yaml
kubectl get deployments

## Blue-Green Deployment

kubectl apply -f services/fortune-app-blue-service.yaml
kubectl create -f deployments/fortune-app-green.yaml
kubectl apply -f services/fortune-app-green-service.yaml
kubectl apply -f services/fortune-app-blue-service.yaml # rollback