# K8s

## Prima

### Deployment
1. Both Pods and ReplicaSets are expected to be tied to specific container images that don’t change. The Deployment object exists to manage the release of new versions.

2. This “rollout” process is specifiable and careful. It waits for a user-configurable amount of time between upgrading individual Pods. It also uses health checks to ensure that the new version of the application is operating correctly, and stops the deployment if too many failures occur.

3. a Deployment is a higher-level kubernetes object that wraps around a Pod and adds features such as self-healing, (auto-)scaling, zero-downtime rollouts, and versioned rollbacks.

4. Behind the scenes, Deployments, DaemonSets and StatefulSets are implemented as controllers that run as watch loops constantly observing the cluster making sure observed state matches desired state.

5. At a high-level, containers are a great way to package applications and dependencies. Pods allow containers to run on Kubernetes and enable co-scheduling and a bunch of other good stuff. ReplicaSets manage Pods and bring self-healing and scaling. Deployments manage ReplicaSets and add rollouts and rollbacks.
- deployment (rollout, rollback)
- - replica set ((auto-)scaling, self-healing)
- - - pod (co-location, resource sharing etc)
- - - - container (build & run)
- - - - - app

6. A Deployment object only manages a single Pod template. However, a Deployments can manage multiple replicas of the same Pod.

7. Structure
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  progressDeadlineSeconds: 60
  replicas: 3
  strategy:
	rollingUpdate:
		maxSurge: 1
		maxUnavailable: 1
	type: RollingUpdate | Recreate
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

# Recreate strategy is identical to the RollingUpdate strategy with maxUnavailable set to 100%.
# Setting maxSurge to 100% is equivalent to a blue/green deployment. The deployment controller first scales the new version up to 100% of the old version. Once the new version is healthy, it immediately scales the old version down to 0%.
# Setting minReadySeconds to 60 indicates that the deployment must wait for 60 seconds after seeing a Pod become healthy before moving onto updating the next Pod.
# To set how long the deployment controller should wait before transitioning into timeout state, use the progressDeadlineSeconds field.
```

8. The two most common operations on a deployment are scaling and application updates.
- kubectl scale
- kubectl rollout (history|status|pause|resume|undo|)
- kubectl rollout undo is equivalent to kubectl rollout undo --to-revision=0.
- more:
```bash
$ kubectl create –f deployment-definition.yml 
$ kubectl get deployments
$ kubectl apply –f deployment-definition.yml
$ kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1 > kubectl rollout status deployment/myapp-deployment # triggers rollout
$ kubectl rollout history deployment/myapp-deployment
$ kubectl rollout undo deployment/myapp-deployment
```

9. Rollout on pod template changes
```yaml
template:
	metadata:
		annotations:
			#change only when container image changes to trigger new rollout
			kubernetes.io/change-cause: "Update to green kuard" 
```

10. Limit revision history
```yaml
spec:
	# We do daily rollouts, limit the revision history to two weeks of
	# releases as we don't expect to roll back beyond that.
	revisionHistoryLimit: 14
```