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