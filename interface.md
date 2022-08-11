# K8s

## Prima

### Operators & Interfaces
1. Extensions to the Kubernetes API server either add new functionality to a cluster or limit and tweak the ways that users can interact with their clusters.

2. Different ways to extend Kubernetes:
- Custom Resource Definitions (CRD)
- Container Network Interface (CNI)
- Container Storage Interface (CSI)
- Container Runtime Interface (CRI)

#### Custom Resource Definitions (CRD)
1. New Resource
- These newAPI objects can be added to namespaces, are subject to RBAC, and can be accessed with existing tools like kubectl as well as via the Kubernetes API.
- It actually is possible to provide an OpenAPI specification for a custom resource
```
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
  metadata:
  name: loadtests.beta.kuar.com
spec:
  group: beta.kuar.com
  versions:
    - name: v1
      served: true
      storage: true
  scope: Namespaced
  names:
    plural: loadtests
    singular: loadtest
    kind: LoadTest
    shortNames:
      - lt
```

2. Patterns for Custom Resources
- Just Data: just a CRD that stores data for application, admission control can optionally be use to validate or mutate the data

3. Compilers
- A slightly more complicated pattern is the “compiler” or “abstraction” pattern. In this pattern the API extension object represents a higher-level abstraction that is “compiled” into a combination of lower-level Kubernetes objects. 

4. Operators
- These extensions likely provide a higher-level abstraction (for example, a database) that is compiled down to a lower-level representation, but they also provide online functionality,such as snapshot backups of the database, or upgrade notifications when a new version of the software is available.

5. Admission Control
- Many other systems usec ustom admission controllers to auto-inject sidecar containers into all Pods created on the system to enable “auto-magic” experiences.

#### Container Network Interface (CNI)
1. Popular Implementation
    - Calico
    - Flannel
    - Romana
    - Weave Net
    - And others

#### Container Storage Interface (CSI)

#### Container Runtime Interface (CRI)

