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

Control Plane (store and manage the state of the cluster)
	 The etcd distributed persistent storage
		- RAFT consensus, 
	 The API server
		- CRUD, optimistic locking
		- authN, authZ, admission control (mutation,validation)
	 The Scheduler
		- All it does is wait for newly created pods through the API server’s watch mechanismand assign a node to each new pod that doesn’t already have the node set.
	 The Controller Manager
			 Replication Manager (a controller for ReplicationController resources)
			 ReplicaSet, DaemonSet, and Job controllers
			 Deployment controller
			 StatefulSet controller
			 Node controller
			 Service controller
			 Endpoints controller
			 Namespace controller
			 PersistentVolume controller
			 Others

Worker Nodes (run application containers/workloads)
	 The Kubelet
		- responsible for everything running on aworker node. Its initial job is to register the node it’s running on by creating a Noderesource in the API server.
		- continuously monitor the API server forPods that have been scheduled to the node, and start the pod’s containers. It does thisby telling the configured container runtime (which is Docker, CoreOS’ rkt, or somethingelse) to run a container from a specific container image. 
		- The Kubelet then constantlymonitors running containers and reports their status, events, and resourceconsumption to the API server.
		- runs the container liveness probes, restartingcontainers when the probes fail. Lastly, it terminates containers when their Pod isdeleted from the API server and notifies the server that the pod has terminated.
	 The Kubernetes Service Proxy (kube-proxy)
	 The Container Runtime (Docker, rkt, or others)

Addons
	 The Kubernetes DNS server
	 The Dashboard
	 An Ingress controller
	 Heapster
	 The Container Network Interface network plugin
	 The Container Storage Interface plugin


- While multiple instances of etcd and API server can be active at thesame time and do perform their jobs in parallel, only a single instance of the Schedulerand the Controller Manager may be active at a given time—with the others instandby mode.
- The Kubelet is the only component that always runs as a regular system component,and it’s the Kubelet that then runs all the other components as pods.
- When the request is only trying to read data, the request doesn’t gothrough the Admission Control.
- You could also run a Kubernetes clusterwithout a Scheduler, but then you’d have to perform the scheduling manually.

$ kubectl get componentstatuses
$ kubectl attach
$ kubectl port-forward



10. Packaging apps for Kubernetes
-  Packaged as a container (image) [image store like docker hub, github]
-  Wrapped in a Pod (static Pods)
-  Deployed via a declarative manifest file (Pod, ReplicaSet, Deployment etc)

11. Playgrounds
- Play with Kubernetes (PWK) labs.play-with-k8s.com
- Katakoda
- Docker Desktop
- minikube
- k3d
- kops
- kubeadm 
- k3s
- k3d

12. Popular Hosted k8s
- AWS - Elastic Kubernetes Service(EKS)
- Azure - Azure Kubernetes Service(AKS)
- Linode - Linode Kubernetes Engine(LKE)
- DigitalOcean - DigitalOcean Kubernetes(DOKS)
- GoogleCloudPlatform - Google Kubernetes Engine(GKE)

13. Data Flow
```
Request -> AuthN -> AuthZ -> AdmissionControl -> API Server -> Store
```