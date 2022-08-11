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


5. Scheduling pods to specific nodes
```yaml
spec:
	nodeSelector:
		gpu: "true" // labels
```

6. Scheduling to one specific node
```yaml
spec:
	nodeName: node02
# OR
spec:
	nodeSelector: kubernetes.io/node-hostname
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