# K8s

## Prima

### Kubelet
1. Introduction
- Kubeadm does not deploy Kubelets
- 
2. Install
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet
```

```bash
ExecStart=/usr/local/bin/kubelet \\ --config=/var/lib/kubelet/kubelet-config.yaml \\ --container-runtime=remote \\ --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock \\ --image-pull-progress-deadline=2m \\ --kubeconfig=/var/lib/kubelet/kubeconfig \\
  --network-plugin=cni \\
  --register-node=true \\
  --v=2
```