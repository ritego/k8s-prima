# K8s
## Prima
### Services
1. Service object operates at Layer 4 (according to the OSI model). This means that it only forwards TCP and UDP connections and doesn’t look inside of those connections.

2. Services provide reliable networking for a set of Pods.
- Stable DNS name, IP address, and port.
- It’s a stable network abstraction point that provides TCP and UDP load-balancing across a dynamic set of Pods
- As they operate at the TCP and UDP layer, they don’t possess application intelligence.

3. Types
- ClusterIP -  A ClusterIP Service has a stable virtual IP address that is only accessible from inside the cluster
- NodePort - NodePort Services build on top of the ClusterIP type and enable external access via a dedicated port on every cluster node. 
- LoadBalancer - LoadBalancer Services make external access even easier by integrating with an internet-facing load-balancer on your underlying cloud platform

4. Services use labels and selectors to dynamically select the Pods to send traffic to.

5. Structure
```yaml
apiVersion: v1 
kind: Service 
metadata:
	name: hello-svc 
spec:
	type: NodePort
	ports:
		- port: 8080 # service port
			nodePort: 30050 # port on node, range 30000 - 32767
			targetPort: 8080 # port on pod/container
			protocol: TCP
	selector:
		app: hello-world 
		env: tkb
```

6. Every time you create a Service, Kubernetes automatically creates an associated Endpoints object. The Endpoints object is used to store a dynamic list of healthy Pods matching the Service’s label selector.

7. Kubernetes clusters run an internal DNS service that is the centre of service discovery. Every service is assigned a name which is registered with the cluster DNS. The cluster DNS, upon query, resolves this name to the corresponding IP address. Kube-proxy also registers the service IP for Load balancing across available Endpoints/Pods.

8. The front-end of a Service provides an immutable IP, DNS name and port that is guaranteed not to change for the entire life of the Service. The back-end of a Service uses labels and selectors to load-balance traffic across a potentially dynamic set of application Pods (represented by Endpoints).

9. EndpointSlices - a moderm and perfomant succesor of Endpoints

10. Service Discovery (SD)
- While the dynamic nature of Kubernetes makes it easy to run a lot of things, it creates problems when it comes to finding those things. Most of the traditional network infrastructure wasn’t built for the level of dynamism that Kubernetes presents. Apps on k8s need a way to find the other apps they work with. This is where service discovery comes into play.

- SD Activitives
	- k8s thru kubelet updates /etc/resolv.conf on deployed pods to point to the cluster DNS service
	- Every service is registered with the cluster DNS
	- any process in any container/pop would then be able to resolve services by name

- There are two major components to service discovery:
	- Registration - post connection details to a service registry so other apps can find it and consume it.
	- Discovery - query the DNS cluster to resolve names to IP

- Kube-proxy creates local networking rules to redirect ClusterIP traffic to Pod IPs. In modern Linux-based Kubernetes clusters, the technology used to create these rules is the Linux IP Virtual Server (IPVS). Older versions use iptables

- It’s important to understand that every cluster has an address space, and that Namespaces partition it. Cluster address spaces are based on a DNS domain that we call the cluster domain. The domain name is usually cluster.local and objects have unique names within it. For example, a Service called ent will have a fully qualified domain name (FQDN) of ent.default.svc.cluster.local. The format is <object-name>.<namespace>.svc.cluster.local. Pods also have The format is <ip>.<namespace>.pod.cluster.local.
