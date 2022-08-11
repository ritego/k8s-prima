# K8s

## Prima

### GitOps/IasCode
1. Use Git filesystem as the source of truth. Rather than viewing the state of the cluster—the data in etcd—as the source of truth, it is optimal to view the filesystem of YAML objects as the source of truth for your application.

2. Code review to ensure the quality of changes

3. Add feature flags for staged roll forward and roll back

4. Structure
```
frontend/
  v1/
    frontend-deployment.yaml
    frontend-service.yaml
  current/
    frontend-deployment.yaml
    frontend-service.yaml
service-1/
  v1/
    service-1-deployment.yaml
    service-1-service.yaml
  v2/
    service-1-deployment.yaml
    service-1-service.yaml
  current/
    service-1-deployment.yaml
    service-1-service.yaml
```
