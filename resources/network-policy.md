# K8s

## Prima

### Network Policy
1. Introduction
- There are two sorts of isolation for a pod: isolation for egress, and isolation for ingress.

2. Solutions that Support Network Policies:
- Kube-router
- Calico
- Romana
- Weave net

3. Solutions that DO NOT Support Network Pol 
- Flannel

4. Structure 
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - ipBlock:
            cidr: 172.17.0.0/16
            except:
              - 172.17.1.0/24
        - namespaceSelector:
            matchLabels:
              project: myproject
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978
```

5. There are four kinds of selectors that can be specified in an ingress from section or egress to section:

- podSelector: This selects particular Pods in the same namespace as the NetworkPolicy which should be allowed as ingress sources or egress destinations.

- namespaceSelector: This selects particular namespaces for which all Pods should be allowed as ingress sources or egress destinations.

- namespaceSelector and podSelector: A single to/from entry that specifies both namespaceSelector and podSelector selects particular Pods within particular namespaces. Be careful to use correct YAML syntax; this policy:

6. AND
```yaml
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          user: alice
      podSelector:
        matchLabels:
          role: client
```

7. OR
```yaml
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          user: alice
    - podSelector:
        matchLabels:
          role: client
```

8. Default Policies
- By default ingress and egress is allowed across all pods
- deny all ingress and egress across all pods
```yaml
spec:
  podSelector: {} # select all pods
  policyTypes: 
  - Ingress
  - Egress
```

- deny all ingress traffic
```yaml
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

- allow all ingress traffic
```yaml
spec:
  podSelector: {}
  ingress:
  - {}
  policyTypes:
  - Ingress
```

- deny all egress traffic
```yaml
spec:
  podSelector: {}
  policyTypes:
  - Egress
```

- allow all egress traffic
```yaml
spec:
  podSelector: {}
  egress:
  - {}
  policyTypes:
  - Egress
```
