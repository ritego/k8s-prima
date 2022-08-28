# K8s

## Prima

### Pod
$ kubectl create -f a-pod.yaml
$ kubectl get po kubia-manual -o yaml
$ kubectl get pods
$ kubectl explain pod.spec
$ kubectl logs kubia-manual [-c kubia]
$ kubectl port-forward kubia-manual 8888:8080

1. Each container within a Pod runs in its own:
- cgroup
    cgroups (abbreviated from control groups) is a Linux kernel feature that limits, accounts for, and isolates the resource usage of a collection of processes.
    Cgroups allow you to allocate resources — such as CPU time, system memory, network bandwidth, or combinations of these resources — among user-defined groups of tasks (processes) running on a system.
- filesystem (fully isolated from other containers)

2. but they share:
- Network namespace (UTS) 
    - UTS (UNIX Time-Sharing) namespaces allow a single system to appear to have different host and domain names to different processes. 
    - IP address and port space 
    - hostname 
- IPC
    - they can communicate through native interprocess communication channels over System V IPC or POSIX message queues (IPC namespace)
- PID namespace
    - In the latest Kubernetes and Docker versions, they can also share the same PID namespace, but that feature isn’t enabled by default.
- All the containers in a pod also have the same loopback network interface, so a container can communicate with other containers in the same pod through localhost.

3. Applications in different Pods are isolated from each other; they have different IP addresses, different hostnames, and more. Containers in different Pods running on the same node might as well be on different servers.


4. Structure
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  schedulerName: "my-custom-scheduler"
  volumes:
    - name: "kuard-data"
      nfs:
        server: my.nfs.server.local
        path: "/exports"
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:blue
      name: kuard
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
      resources:
        requests:
          cpu: "500m"
          memory: "128Mi"
        limits:
          cpu: "1000m"
          memory: "256Mi"
      volumeMounts:
        - mountPath: "/data"
          name: "kuard-data"
      livenessProbe:
        httpGet:
          path: /healthy
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3
      readinessProbe:
        httpGet:
          path: /ready
          port: 8080
        initialDelaySeconds: 30
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3
```


5. Scheduling pods to specific nodes using node labels
```yaml
spec:
	nodeSelector:
		gpu: "true" // labels
```

6. Scheduling to one specific node using node name
```yaml
spec:
	nodeName: node02
```

7. Scheduling using node affinity
- requiredDuringSchedulingIgnoredDuringExecution: The scheduler can't schedule the Pod unless the rule is met. This functions like nodeSelector, but with a more expressive syntax.
- preferredDuringSchedulingIgnoredDuringExecution: The scheduler tries to find a node that meets the rule. If a matching node is not available, the scheduler still schedules the Pod.
```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: topology.kubernetes.io/zone
            operator: In
            values:
            - antarctica-east1
            - antarctica-west1
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
```

8. Intra-pod affinity
- Inter-pod affinity and anti-affinity rules take the form "this Pod should (or, in the case of anti-affinity, should not) run in an X if that X is already running one or more Pods that meet rule Y"
```yaml
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: topology.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: topology.kubernetes.io/zone
```

7. Configure Pods/Containers
```yaml
spec:
  containers:
  - name: app
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
        custom.com/resource: "2"
      limits:
        memory: "128Mi"
        cpu: "500m"
        custom.com/resource: "3"
```
- resources
	- requests
		- cpu
		- memory
		- custom.com/resource
	- limits
		- cpu
		- memory
		- custom.com/resource

8. Topology Constraint
```yaml
kind: Pod
apiVersion: v1
metadata:
  name: my-pod
  labels:
    foo: bar
spec:
  topologySpreadConstraints:
  - maxSkew: 1 # The maximum difference in number of pods between any two topology domains. The default is 1, and you cannot specify a value of 0
    topologyKey: topology.kubernetes.io/zone # The key of a node label. Nodes with this key and identical value are considered to be in the same topology.
    whenUnsatisfiable: DoNotSchedule | ScheduleAnyway # How to handle a pod if it does not satisfy the spread constraint. 
    labelSelector: # pods to be spread
      matchLabels:
        foo: bar
  containers:
  - image: "docker.io/ocpqe/hello-pod"
    name: hello-pod

# For example, the above matches on pods labeled foo:bar, distributes among zones, specifies a skew of 1, and does not schedule the pod if it does not meet these requirements.
```

### PriorityClass
1. To use priority and preemption:
  - Add one or more PriorityClasses
  - Create Pods with priority ClassName set to one of the added PriorityClasses. Of course you do not need to create the Pods directly; normally you would add priorityClassName to the Pod template of a collection object like a Deployment.

2. Structure
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false # if true, pods with no priority class name gets this value, else they get zero
preemptionPolicy: Never # will be scheduled ahead of other queued pods, as soon as sufficient cluster resources "naturally" become free
description: "This priority class should be used for XYZ service pods only."
```

3. Add priority class to pod
```yaml
spec:
  priorityClassName: high-priority
```