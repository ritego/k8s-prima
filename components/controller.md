# K8s

## Prima

### (Workload) Controllers
1. Controllers ensure the observed state of the cluster matches the desired state:
- Obtain desired state 
- Observe current state 
- Determine differences
- Reconcile differences

2. Types:
- Deployment controller
- StatefulSet controller 
- ReplicaSet controller 
- Namespace controller 
- Endpoint controller 
- Job controller 
- CronJob controller 
- Node controller 
- Replication controller 
- PV controller 
- etc

3. Controller, control loop, watch loop, and reconciliation loop mean the same thing.

4. Key Variables
- Node Monitor Period
- Node Monitor Grace Period
- POD Eviction Timeout

5. Install
- Download
```bash
$   wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-controller-manager
```

- Install
```bash
ExecStart=/usr/local/bin/kube-controller-manager \\
--address=0.0.0.0 \\
--cluster-cidr=10.200.0.0/16 \\
--cluster-name=kubernetes \\ --cluster-signing-cert-file=/var/lib/kubernetes/ca.pem \\ --cluster-signing-key-file=/var/lib/kubernetes/ca-key.pem \\ --kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\ --leader-elect=true \\
--root-ca-file=/var/lib/kubernetes/ca.pem \\ --service-account-private-key-file=/var/lib/kubernetes/service-account-key.pem \\ --service-cluster-ip-range=10.32.0.0/24 \\
--use-service-account-credentials=true \\
--v=2
Node Monitor Period = 5s
--node-monitor-period=5s
--controllers stringSlice Default: [*]
```