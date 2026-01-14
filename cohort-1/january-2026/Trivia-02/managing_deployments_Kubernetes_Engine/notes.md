# Notes & Observations

## Deployment Behavior
- A Deployment manages a ReplicaSet
- ReplicaSets maintain the desired replica count
- Deployments enable declarative specification of application versions

## Scaling
- Scaling adjusted Pod count without downtime
- ReplicaSet size changed from 3 → 5 → 3 during testing

## Rolling Update Insights
- Image tag changes triggered replacement of Pods
- Kubernetes replaced Pods gradually to avoid service disruption
- `kubectl rollout pause/resume` allowed partial rollout examination
- Rollback successfully reverted to a stable image

## Canary Deployment Takeaways
- Service selector matched both blue & canary deployments
- Traffic distribution favored existing version with occasional new responses
- Canary pattern is useful for real user validation with low blast radius

## Blue-Green Deployment Takeaways
- Two parallel deployments existed (blue=1.0, green=2.0)
- Traffic switch occurred at the Service layer via selector update
- Rollback required only re-applying the previous Service selector

## Applied DevOps Concepts
- Declarative manifests
- Versioned releases
- Controlled rollout
- Real-time rollback strategies

## Overall Learning
This reinforced how Kubernetes supports modern CI/CD and progressive delivery patterns without disrupting running workloads.
