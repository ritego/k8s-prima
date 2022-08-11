# K8s

## Prima

### The API server
1. Kubernetes is API-centric. The API server is the front-end into the control plane and all instructions and communication pass through it. By default, it exposes a RESTful endpoint on port 443.

2. All of the following make CRUD-style requests to the API server (create, read, update, delete).
- Operators and developers using kubectl
- Pods
- Kubelets
- Control plane services
- More…

3. Cluster connection details and credentials are stored in a kubeconfig file.
   - Windows: C:\Users\<user>\.kube\config
   - Linux/Mac: /home/<user>/.kube/config

4. In this chapter, you learned that all requests to the API server include credentials and pass through authentication, authorization, and admission control. The connection between the client and the API server is also secured with TLS.

5. The API server is a Kubernetes control plane service. This usually means it runs as a set of Pods in the kube-system Namespace on the control plane nodes of your cluster. If you build and manage your own Kubernetes clusters,you need to make sure the control plane is highly-available and has enough performance to keep the API server up-and-running and responding quickly to requests.

6. It’s common for the API server to be exposed on port 443 or 6443,

7. The high-level pattern for extending the API involves two main things:
- Writing your custom controller
- Creating the custom resource: Kubernetes has a CustomResourceDefinition (CRD) object that lets you create new resources in the API that look, smell, and feel like native Kubernetes resources. But a custom resource doesn’t do anything until you create a custom controller to go with it.

8. Install
-   Donwload
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver
```

- Setup
```bash
ExecStart=/usr/local/bin/kube-apiserver \\ ExecStart=/usr/local/bin/kube-apiserver \\
--advertise-address=${INTERNAL_IP} \\ --advertise-address=${INTERNAL_IP} \\ --allow-privileged=true \\ --allow-privileged=true \\
--apiserver-count=3 \\
--apiserver-count=3 \\ --authorization-mode=Node,RBAC \\ --authorization-mode=Node,RBAC \\ --bind-address=0.0.0.0 \\ --bind-address=0.0.0.0 \\
--enable-admission- --client-ca-file=/var/lib/kubernetes/ca.pem \\
plugins=Initializers,NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,Reso --enable-admission-
urceQuota \\ plugins=Initializers,NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,Reso
--enable-swagger-ui=true \\ urceQuota \\
 --etcd-servers=https://127.0.0.1:2379 \\
--enable-swagger-ui=true \\
--event-ttl=1h \\
--etcd-cafile=/var/lib/kubernetes/ca.pem \\ --experimental-encryption-provider-config=/var/lib/kubernetes/encryption-config.yaml \\ --etcd-certfile=/var/lib/kubernetes/kubernetes.pem \\
  --runtime-config=api/all \\
--etcd-keyfile=/var/lib/kubernetes/kubernetes-key.pem \\ --service-account-key-file=/var/lib/kubernetes/service-account.pem \\ --etcd-servers=https://127.0.0.1:2379 \\
--service-cluster-ip-range=10.32.0.0/24 \\
--event-ttl=1h \\
--service-node-port-range=30000-32767 \\ --experimental-encryption-provider-config=/var/lib/kubernetes/encryption-config.yaml \\ --v=2
```