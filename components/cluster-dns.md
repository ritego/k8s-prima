# K8s

## Prima

### Cluster DNS
1. Kubernetes provides a well-known internal DNS service that is usually call the “cluster DNS”. It’s well known because every Pod in the cluster knows where to find it. 

2. It’s implemented in the kube-system Namespace as a set of Pods managed by a Deployment called coredns. These Pods are fronted by a Service called kube-dns. Behind the scenes, it’s based on a DNS technology called CoreDNS and runs as a Kubernetes-native application.

3. Provides naming and discovery for the services that are defined in the cluster. 

4. With Kubernetes 1.12, Kubernetes transitioned from the kube-dns DNS server to the core-dns DNS server. Because of this, if you are running an older Kubernetes cluster, you may see kube-dns instead.

5. Structure
- CoreDNS
- - Container
- - - Pods
- - - - Replicaset
- - - - - Deployment
- - - - - - Service (load balance)

6. Point pods to the cluster dns server
- add entry to /etc/resolv.conf (done when the pod is deployed)?
- - point to DNS server
- - nameserver 10.96.0.10
- - search xyz.svc.cluster.local svc.cluster.local cluster.local 