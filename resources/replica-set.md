# K8s

## Prima

### ReplicaSet
1. A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

2. Why?
- Redundancy: Multiple running instances mean failure can be tolerated.
- Scale: Multiple running instances mean that more requests can be handled.
- Sharding: Different replicas can handle different parts of a computation inparallel.

3. Structure
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

4. A ReplicaSet identifies new Pods to acquire by using its selector. If there is a Pod that has no OwnerReference or the OwnerReference is not a Controller and it matches a ReplicaSet's selector, it will be immediately acquired by said ReplicaSet.

5. Reconciliation Loops
- The central concept behind a reconciliation loop is the notion of desired state versus observed or current state.
- manges pods using reconciliation loop.

6.The ReplicaSet controller adds anannotation to every Pod that it creates. The key for the annotation is kubernetes.io/created-by.

7. ReplicaSets are the successors to ReplicationControllers. The two serve the same purpose, and behave similarly, except that a ReplicationController does not support set-based selector requirements as described in the labels user guide. As such, ReplicaSets are preferred over ReplicationControllers

8. When scaling down, the ReplicaSet controller chooses which pods to delete by sorting the available pods to prioritize based on the following general algorithm:
    - Pending (and unschedulable) pods are scaled down first
    - If controller.kubernetes.io/pod-deletion-cost annotation is set, then the pod with the lower value will come first.
    - Pods on nodes with more replicas come before pods on nodes with fewer replicas.
    - If the pods' creation times differ, the pod that was created more recently comes before the older pod (the creation times are bucketed on an integer log scale when the LogarithmicScaleDown feature gate is enabled)
    - If all of the above match, then selection is random.