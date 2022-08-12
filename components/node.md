# K8s

## Prima

### Node
1. Control Plane
2. Worker Nodes
    - does three things:
        - Watch the API server for new work assignments
        - Execute work assignments
        - Report back to the control plane (via the API server)
3. Add label to nodes 
- kubectl label nodes k0-default-pool-35609c18-z7tb ssd=true

4. Taints
- Taint a node with key, value
```
$ kubectl taint nodes node-name key=value:taint-effect
```

- Empower pods to tolerate the taint with same key, value
```yaml 
tolerations:
    - key:
      operator: "Equal"
      value: ""
      effect:
      tolerationSeconds: 6000 # define how long that Pod stays bound to a failing or unresponsive Node.
``` 

- taint-effect defines what happens to PODs that do not tolerate this taint?
    - NoSchedule: This means that no pod will be able to schedule onto the node unless it has a matching toleration
    - PreferNoSchedule: the system will try to avoid placing a pod that does not tolerate the taint on the node
    - NoExecute: the pod will be evicted from the node, if it is already running on the node

5. Built in taints
- node.kubernetes.io/not-ready
- node.kubernetes.io/unreachable
- node.kubernetes.io/memory-pressure
- node.kubernetes.io/disk-pressure
- node.kubernetes.io/pid-pressure
- node.kubernetes.io/network-unavailable
- node.kubernetes.io/unschedulableun.
- node.cloudprovider.kubernetes.io/uninitialized