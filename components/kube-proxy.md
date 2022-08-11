# K8s

## Prima

### Kube Proxy
1. The Kubernetes proxy is responsible for routing network traffic to load balanced services in the Kubernetes cluster. 

2. To do its job, the kube-proxy container should be running on all nodes in a cluster. usually as a DaemonSet.

3. The Kubernetes network proxy runs on each node. This reflects services as defined in the Kubernetes API on each node and can do simple TCP, UDP, and SCTP stream forwarding or round robin TCP, UDP, and SCTP forwarding across a set of backends. Service cluster IPs and ports are currently found through Docker-links-compatible environment variables specifying ports opened by the service proxy. There is an optional addon that provides cluster DNS for these cluster IPs. The user must create a service with the apiserver API to configure the proxy.

4. Install
- Download
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy
```

- Setup
```bash
ExecStart=/usr/local/bin/kube-proxy \\ --config=/var/lib/kube-proxy/kube-proxy-config.yaml
Restart=on-failure
RestartSec=5
```