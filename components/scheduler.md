# K8s

## Prima

### Scheduler
1. The scheduler isn’t responsible for running tasks, just picking the nodes to run them. A task is normally a Pod/container.

2. Decisions based on:
- Filter nodes
    - Resource Requirements/Requests and Limits
    - Taints and Tolerations
    - Node Selectors/Affinity
- Ranking by number of existing pods

3. Install
- Download
```bash
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler
```
- Install
```bash
$ ExecStart=/usr/local/bin/kube-scheduler \\ 
--config=/etc/kubernetes/config/kube-scheduler.yaml \\ 
--v=2
```