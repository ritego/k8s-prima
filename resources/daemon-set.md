# K8s

## Prima

### DaemonSet
1. A DaemonSet ensures a copy of a Pod is running across a set of nodes in a Kubernetes cluster. DaemonSets are used to deploy system daemons such as log collectors and monitoring agents, which typically must run on every node. 

2. DaemonSets share similar functionality with ReplicaSets, both create Pods that are expected to be long-running services and ensure that the desired state and the observed state of the cluster match.

3. Notes:
- You can use labels to run DaemonSet Pods on specific nodes only
- You can also use DaemonSets to install software on nodes in a cloud based cluster.
- You can even mount the host filesystem and run scripts that install RPM/DEB packages onto the host operating system.
- By default a DaemonSet will create a copy of a Pod on every node unless a node selector is used
- DaemonSets determine which node a Pod will run on at Pod creation time by specifying the nodeName field in the Pod spec. As a result, Pods created by DaemonSets are ignored by the Kubernetes scheduler.

4. Reconciliation control loop
DaemonSets are managed by a reconciliation control loop that measures the desired state (a Pod is present on all nodes) with the observed state (is the Pod present on a particular node). Given this information, the DaemonSet controller creates a Pod on each node that doesn’t currently have a matching Pod.

5. Structure
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  updateStrategy:
    type: RollingUpdate
    rollingUpdate:
        maxUnavailable: 1
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # these tolerations are to have the daemonset runnable on control plane nodes
      # remove them if your control plane nodes should not run pods
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
      - key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
```

6. Alternatives
- Init scripts (e.g init, systemd)
- bare pods for each node
- static pods for each node

7. Add pods to specific nodes only
```yaml
- spec:
		template:
			spec:
				nodeSelector:
						ssd: "true"
```

8. RollingUpdate Config
There are two parameters that control the rolling update of a DaemonSet:
- spec.minReadySeconds, which determines how long a Pod must be “ready” before the rolling update proceeds to upgrade subsequent Pods
- spec.updateStrategy.rollingUpdate.maxUnavailable which indicates how many Pods may be simultaneously updated by the rolling update

9. Common Usecases
- kube proxy
- networking plugins (e.g weavenet)

10. Scheduling
- Can be controlled using 
  - NodeAffinity 
  - default/multiple scheduler

11. Install
- Download
```bash
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-scheduler
```

- Install
```bash
ExecStart=/usr/local/bin/kube-scheduler \\ --config=/etc/kubernetes/config/kube-scheduler.yaml \\
--scheduler-name= default-scheduler

ExecStart=/usr/local/bin/kube-scheduler \\ --config=/etc/kubernetes/config/kube-scheduler.yaml \\
--scheduler-name= my-custom-scheduler
```