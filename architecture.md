# K8s

## Prima

### Architecture
1. Master
- manage, plan, schedule, monitor
- parts
    - Api Server (x)
    - Controller (x-1)
    - Scheduler (x-1)
    
    - ETCD
    - Cluster DNS

2. Worker
- run workload, host apps in containers
- parts:
    - Kubelet (x-2)
    - Kube-proxy (x-2)

    - container runtime (CRI)

1. Kubernetes Architecture 
2. ETCD For Beginners 
3. ETCD in Kubernetes 
4. Kube-API Server 
5. Controller Managers 
6. Kube Scheduler
7. Kubelet 
8. Kube Proxy


### Cluster Installation
1. Consideration
- Node Considerations
- Resource Requirements
- Network Considerations

2. Ask
- Purpose
    - Education
    - Development & Testing
    - Hosting Production Applications
- Cloud or OnPrem? 
- Workloads
    - How many?
    - What kind?
        - Web
        - Big Data/Analytics
- Application Resource Requirements
    - CPU Intensive
    - Memory Intensive
- Traffic
    - Heavy traffic
    - Burst Traffic

### Cluster Upgrade
1. Plan
    - kubeadm upgrade plan
        - see plan
        - copy the apply command
        - copy the accepted kubeadm version
2. Upgrade KubeAdmin
    - get version from step 1
    - apt-get upgrade -y kubeadm=1.12.0-00
3. Upgrade cluster
    - use the command from step 1
    - e.g kubeadm upgrade apply v1.12.0
4. Upgrade the nodes kubelet, 1-by-1
    - kubectl get nodes
    - ssh [nodename/ip]
    - get version from step 1
    - kubectl drain node-1
    - kubectl cordon node-1
    - kubectl uncordon node-1
    - apt-get upgrade -y kubelet=1.12.0-00
    - systemctl restart kubelet
 
 ### Backups
 1. Resource Configs
 ```
  kubectl get all --all-namespaces -o yaml > all-deploy-services.yaml
 ```

 2. ETCD
 - Backup
 get data dir from installation config 
    - from /etc/kubernetes/manifest or /etc/systemd/systems or ps aux | grep 
    - e.g data-dir=/var/lib/etcd

```bash
$ ETCDCTL_API=3 etcdctl \
     snapshot save snapshot.db

$ ETCDCTL_API=3 etcdctl \
     snapshot status snapshot.db
```

- Restore
```bash
$ service kube-apiserver stop
$  ETCDCTL_API=3 etcdctl \
snapshot restore snapshot.db \
--data-dir /var/lib/etcd-from-backup \
--initial-cluster master-1=https://192.168.5.11:2380,master-2=https://192.168.5.12:2380 \ 
--initial-cluster-token etcd-cluster-1 \
--initial-advertise-peer-urls https://${INTERNAL_IP}:2380
```
