# K8s

## Prima

### Pod
1. Introduction
- Horizontal Pod Autoscaling (HPA) i.e scaling based on CPU and/or memory
- $ kubectl autoscale rs frontend --max=10 --min=3 --cpu-percent=50 (This command creates an autoscaler that scales between two and five replicas with a CPU threshold of 80%
- HPA requires the presence of the heapster Pod on your cluster. heapster keepstrack of metrics and provides an API for consuming metrics that HPA uses when making scaling decisions. Most installations of Kubernetes include heapster by default. You can validate its presence by listing the Pods in the kube-system namespace. You should see a Pod named heapster somewhere in that list. If you do not see it, autoscaling will not work correctly.
- horizontal scaling, which involves creating additional replicas of a Pod, and vertical scaling, which involves increasing the resources required for a particular Pod
- Vertical scaling is not currently implemented in Kubernetes, but it is planned.

2. Structure
```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: frontend-scaler
spec:
  scaleTargetRef:
    kind: ReplicaSet
    name: frontend
  minReplicas: 3
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```