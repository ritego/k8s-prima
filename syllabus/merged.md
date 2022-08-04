# K8s

## Prima

### Every K8s
CKAD
Application Design and Build 20
- [x] Define, build and modify container images
- [x] Understand Jobs and CronJobs
- [x] Understand multi-container Pod design patterns (e.g. sidecar, init and others)
- [x] Utilize persistent and ephemeral volumes

Application Deployment 20
- [x] Use Kubernetes primitives to implement common deployment strategies (e.g. blue/ green or canary)
- [x] Understand Deployments and how to perform rolling updates
- [ ] Use the Helm package manager to deploy existing packages

Application observability and maintenance 15
- [ ] Understand API deprecations
- [ ] Implement probes and health checks
- [ ] Use provided tools to monitor Kubernetes applications
- [x] Utilize container logs
- [x] Debugging in Kubernetes

Application Environment, Configuration and Security 25
- [ ] Discover and use resources that extend Kubernetes (CRD)
- [x] Understand authentication, authorization and admission control
- [x] Understanding and defining resource requirements, limits and quotas
- [x] Understand ConfigMaps
- [x] Create & consume Secrets
- [x] Understand ServiceAccounts
- [x] Understand SecurityContexts

Services & Networking 20
- [x] Demonstrate basic understanding of NetworkPolicies
- [] Provide and troubleshoot access to applications via services
- x ] Use Ingress rules to expose applications
 
Cluster Architecture, Installation & Configuration 25
- [x] Manage role based access control (RBAC)
- [ ] Use Kubeadm to install a basic cluster
- [x] Manage a highly-available Kubernetes cluster
- [x] Provision underlying infrastructure to deploy a Kubernetes cluster
- [ ] Perform a version upgrade on a Kubernetes cluster using Kubeadm 
- [ ] Implement etcd backup and restore

Workloads & Scheduling 15
- [x] Understand deployments and how to perform rolling update and rollbacks
- [x] Use ConfigMaps and Secrets to configure applications
- [x] Know how to scale applications
- [x] Understand the primitives used to create robust, self-healing, application deployments 
- [ ] Understand how resource limits can affect Pod scheduling
- [ ] Awareness of manifest management and common templating tools

Services & Networking 20
- [x] Understand host networking configuration on the cluster nodes
- [x] Understand connectivity between Pods
- [x] Understand ClusterIP, NodePort, LoadBalancer service types and endpoints 
- [x] Know how to use Ingress controllers and Ingress resources
- [x] Know how to configure and use CoreDNS
- [x] Choose an appropriate container network interface plugin

Storage 10
- [x] Understand storage classes, persistent volumes
- [x] Understand volume mode, access modes and reclaim policies for volumes 
- [x] Understand persistent volume claims primitive
- [x] Know how to configure applications with persistent storage

Troubleshooting 30
- [x] Evaluate cluster and node logging
- [x] Understand how to monitor applications 
- [x] Manage container stdout & stderr logs
- [x] Troubleshoot application failure
- [x] Troubleshoot cluster component failure 
- [x] Troubleshoot networking

CKS
Cluster Setup 10
- [ ] Use Network security policies to restrict cluster level access
- [ ] Use CIS benchmark to review the security configuration of Kubernetes components (etcd, kubelet, kubedns, kubeapi)
- [ ] Properly set up Ingress objects with security control 
- [ ] Protect node metadata and endpoints
- [ ] Minimize use of, and access to, GUI elements
- [ ] Verify platform binaries before deploying

Cluster Hardening 15
- [ ] Restrict access to Kubernetes API
- [x] Use Role Based Access Controls to minimize exposure
- [ ] Exercise caution in using service accounts e.g. disable defaults, minimize permissions on newly created ones
- [ ] Update Kubernetes frequently

System Hardening 15
- [ ] Minimize host OS footprint (reduce attack surface)
- [ ] Minimize IAM roles
- [ ] Minimize external access to the network
- [ ] Appropriately use kernel hardening tools such as AppArmor, seccomp

Minimize Microservice Vulnerabilities 20
- [ ] Setup appropriate OS level security domains e.g. using PSP, OPA, security contexts
- [ ] Manage kubernetes secrets
- [ ] Use container runtime sandboxes in multi-tenant environments (e.g. gvisor, kata containers) 
- [ ] Implement pod to pod encryption by use of mTLS

Supply Chain Security 20
- [ ] Minimize base image footprint
- [ ] Secure your supply chain: whitelist allowed image registries, sign and validate images 
- [ ] Use static analysis of user workloads (e.g. kubernetes resources, docker files)
- [ ] Scan images for known vulnerabilities

Monitoring, Logging and Runtime Security 20
- [ ] Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities
- [ ] Detect threats within physical infrastructure, apps, networks, data, users and workloads 
- [ ] Detect all phases of attack regardless where it occurs and how it spreads
- [ ] Perform deep analytical investigation and identification of bad actors within environment 
- [ ] Ensure immutability of containers at runtime
- [ ] Use Audit Logs to monitor access

KCNA
Kubernetes Fundamentals 46
- [x] Kubernetes Resources 
- [x] Kubernetes Architecture 
- [x] Kubernetes API
- [x] Containers
- [x] Scheduling

Cloud Native Observability 8
- [ ] Telemetry & Observability 
- [ ] Prometheus
- [ ] Cost Management

Container Orchestration 22
- [x] Container Orchestration Fundamentals
- [ ] Runtime
- [ ] Security
- [ ] Networking
- [ ] Service Mesh 
- [x] Storage

Cloud Native Architecture 16
- [ ] Cloud Native Architecture Fundamentals 
- [ ] Autoscaling
- [ ] Serverless
- [ ] Community and Governance
- [ ] Personas
- [ ] Open Standards

Cloud Native Application Delivery 8
- [ ] Application Delivery Fundamentals 
- [ ] GitOps
- [ ] CI/CD