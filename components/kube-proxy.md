# K8s

## Prima

### Kube Proxy
The Kubernetes proxy is responsible for routing network traffic to loadbalancedservices in the Kubernetes cluster. To do its job, the proxy mustbe present on every node in the cluster.

the kube-proxy container should berunning on all nodes in a cluster. usually as a DaemonSet