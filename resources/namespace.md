# K8s

## Prima

### Namespace
1. Namespaces are a native way to divide a single Kubernetes cluster into multiple virtual clusters.

2.  Kernel namespaces divide operating systems into virtual operating systems called containers. Kubernetes Namespaces divide Kubernetes clusters into virtual clusters called Namespaces.

3. Its an easy way to apply quotas and policies to groups of objects. They’re not designed for strong workload isolation. Each Namespace can have its own users and RBAC rules, as well as resource quotas.

4. Built-in namespace
- kube-system - dns, metrics server, control plane
- default - default
- kube-public - objects readable by everyone
- kube-node-lease - node heartbeat, management leases

5. while most types of resources are namespaced, a few aren’t. 
- Node - global and not tied to a single namespace

$ kubectl get ns
$ kubectl get po --namespace kube-system
$ kubectl create namespace custom-namespace
$ kubectl create -f kubia-manual.yaml -n custom-namespace

6. If several users or groups of users are using the same Kubernetes cluster, and they each manage their own distinct set of resources, they should each use their own namespace. This way, they don’t need to take any special care not to in advertently modify or delete the other users’ resources and don’t need to concern themselves with name conflicts.


7. Structure
```yaml
apiVersion: v1
kind: Namespace
metadata:
	name: custom-namespace
```