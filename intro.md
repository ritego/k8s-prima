# K8s

## Prima

### Introduction
1. Kubernetes (greek, Helmsman) is an application deployment orchestrator. it’s like an operating system (OS) for the cloud.

2. For the most part, it orchestrates containerized cloud-native micro-services apps:
- An orchestrator is a system that deploys and manages applications.
- A containerized application is an app that runs in a container.
- A cloud-native application is one that’s designed to meet cloud-like demands of auto-scaling, self-healing, rolling updates, rollbacks and more.
- A micro-services app is built from lots of independent small specialised parts that work together to form a meaningful application. Building applications this way is vital for cloud-native features.

3. In the same way that Linux abstracts the hardware differences between server platforms, Kubernetes abstracts the differences between different private and public clouds/datacenter resources.

4. At a high-level, a cloud or datacenter is a pool of compute, network and storage resources.

5. Introduced in 2014
Google had a couple of in-house proprietary systems called Borg and Omega. Google created and donated donated it to the newly formed Cloud Native Computing Foundation (CNCF) in 2014, it’s written in Go (Golang). Apache 2.0 license.

6. Why k8s
- systems are distributed
- systems needs to be reliable, k8s provides self-healing
- systems needs to be stable (highly available, no failure), k8s provides rolling updates, rollbacks 
- systems needs to be scalable (no failure), k8s provides auto-scaling

7. Also, k8s provides:
- Velocity
    -  infrastructure immutability
    -  declarative config
- Scaling (of both software and teams)
    -  decoupled architecture
    -  scale teams with microservice
    -  separation of concern
- Abstracting your infrastructure
- Efficiency

8. A typical computer is a collection of CPU, memory, storage, and networking. On Kubernetes, You package the app as a container, give it a Kubernetes manifest, and let Kubernetes take care of deploying it and keeping it running. You also get a rich set of tools and APIs that let you introspect (observe and examine) it.

9. Components
- nodes (physical, VM, cloud instances)
-  control plane [brain] [Masters, Heads or Head nodes] 
   - The API server (RESTful, HTTPS)
   - The cluster store (etcd)
   - The controller manager (controller of controllers) and controllers
   - The scheduler (for assigning workload to nodes)
   - ? The cloud controller manager
-  worker nodes [muscle] (where user applications run) 
   - Kubelet
   - Container runtime (Container Runtime Interface (CRI))
   - Kube-proxy
-  cluster DNS