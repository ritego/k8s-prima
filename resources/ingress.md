# K8s

## Prima

### Ingress
1. Kubernetes calls its HTTP-based, Layer 7 load-balancing system Ingress. This is similar to traditional reverse proxy based on virtual host like apache or nginx.

2. Ingress operates at layer 7 of the OSI model, also known as the “application layer”. This means it has awareness of HTTP headers, and can inspect them and forward traffic based on hostnames and paths.

3. NodePorts only work on high port numbers (30000-32767) and require knowledge of node names or IPs. LoadBalancer Services fix this, but require a 1-to-1 mapping between an internal Service and a cloud load-balancer. This means a cluster with 25 internet-facing apps will need 25 cloud load-balancers, and cloud load-balancers aren’t cheap. They may also be a finite resource – you may be limited to how many cloud load-balancers you can provision. Ingress fixes this by exposing multiple Services through a single cloud load-balancer.

4. Ingress exposes multiple ClusterIP Services through a single cloud load-balancer. Uses hostnames and paths to make intelligent routing decisions.

5. A cluster can have multiple ingress controllers. The way Kubernetes knows which Ingress controller to use when you deploy an Ingress object is via Ingress classes.

6. Structure
```yaml
apiVersion: networking.k8s.io/v1 
kind: Ingress
metadata:
	name: mcu-all 
	annotations: nginx.ingress.kubernetes.io/rewrite-target: / 
spec:
	ingressClassName: nginx   
    tls:
    - hosts:
        - https-example.foo.com
      secretName: testsecret-tls
	rules:
		- host: shield.mcu.com
			http: 
				paths:
				- path: / 
					pathType: Prefix | Exact | ImplementationSpecific
					backend:
						service:
							name: svc-shield 
							port:
								number: 8080
```

7. Ingress Controller 
- Ingress resource requires ingress controller to work
- Specifically, it is split into a common resource specification and a controller implementation. There is no “standard” Ingress controller that is built into Kubernetes, so the user must install one of many optional implementations.
- Types
    - Contour - This is a controller built to configure the open source (and CNCF project) load balancer called Envoy. Envoy is built to be dynamically configured via an API. The Contour Ingress controller takes care of translating the Ingress objects into something that Envoy can understand.  https://github.com/heptio/contour
    - NGINX ingress controller
    - Ambassador and Gloo are two other Envoy-based Ingress controllers that are focused on being API gateways.
    - Traefik is a reverse proxy implemented in Go that also can function as an Ingress controller. It has a set of features and dashboards that are very developer-friendly

8. Ingress was also created before the idea of a Service Mesh (exemplified by projects such as Istio and Linkerd) was well known. The intersection of Ingress and Service Meshes is still being defined.


4. Types
- Ingress backed by a single Service
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: test-ingress
spec:
  defaultBackend:
    service:
      name: test
      port:
        number: 80
```
- Simple fanout
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: simple-fanout-example
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /foo
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 4200
      - path: /bar
        pathType: Prefix
        backend:
          service:
            name: service2
            port:
              number: 8080
```
- Name based virtual hosting
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: name-virtual-host-ingress
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: service1
            port:
              number: 80
  - host: bar.foo.com
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: service2
            port:
              number: 80
```