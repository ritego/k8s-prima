# K8s

## Prima

### AuthN & AuthZ
1. Intro
- Password based authentication disabled 
- SSH Key based authentication using certs

2. Who can access?
- Files – Username and Passwords
- Files – Username and Tokens
- Certificates
- External Authentication providers e.g LDAP 
- Service Accounts

3. What can they do?
- RBAC Authorization 
- ABAC Authorization 
- Node Authorization 
- Webhook Mode

5. AuthN Modes
- Static Password Files (handled as basic auth)
```yaml
spec: 
	containers: 
	- command:
	- kube-apiserver 
	- --authorization-mode=Node,RBAC
	- --basic-auth-file=xyz.scv

# xyz.scv
# password123,user1,u0001,group1
# password123,user2,u0002,group1
```

- Static Token Files (handled as bearer token)
```yaml
spec: 
	containers: 
	- command:
	- kube-apiserver 
	- --authorization-mode=Node,RBAC
	- --token-auth-file=xyz.csv

# xyz.scv
# KpjCVbI7rCFAHYPkByTIzRb7gu1cUc4B,user10,u0010,group1 
# rJjncHmvtXHc6MlWQddhtvNyyhgTdxSC,user11,u0011,group2
```

- Certs


### RBAC
1. Every request that comes to Kubernetes is associated with some identity. Even a request with no identity is associated with the system:unauthenticated group.

2. Kubernetes makes a distinction between user identities and service account identities. 
- Service accounts are created and managed by Kubernetes itself and are generally associated with components running inside the cluster. 
- User accounts are all other accounts associated with actual users of the cluster, and often include automation like continuous delivery as a service that runs outside of the cluster.

3. authentication providers:
- HTTP Basic Authentication (largely deprecated)
- x509 client certificates
- Static token files on the host
- Cloud authentication providers like Azure Active Directory and AWS Identity and Access Management (IAM)
- Authentication webhooks

- The authentication layer is responsible for validating the identity of requests. Client certificates are commonly used, and integration with AD and other IAM services is recommended for production clusters. Kubernetes does not have its own identity database, meaning it doesn’t store or manage user accounts. The authorization layer checks whether the authenticated user is authorized to carry out the action in the request.This layer is also pluggable and the most common module is RBAC.
    - AuthN
    - AuthZ
    - Admission control (kicks in after authorization and is responsible for enforcing policies)
        - Validating admission controllers reject requests if they don’t conform to policy, whereas 
        - Mutating admission controllers can modify requests to enforce policies.

** Requests to read state are not subjected to admission control.

4.  RBAC comprises 4 objects that define permissions and assign them to users.
- Roles
```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
	namespace: default
	name: pod-and-services
rules:
	- apiGroups: [""]
		resources: ["pods", "services"]
		verbs: ["create", "delete", "get", "list", "patch", "update","watch"]
```

- RoleBinding
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
	namespace: default
	name: pods-and-services
subjects:
	- apiGroup: rbac.authorization.k8s.io
		kind: User
		name: alice
	- apiGroup: rbac.authorization.k8s.io
		kind: Group
		name: mydevs
roleRef:
	apiGroup: rbac.authorization.k8s.io
	kind: Role
	name: pod-and-services
```

- ClusterRole (same as Role but with larger scope)
- ClusterRoleBinding (same as RoleBinding but with larger scope)

5. Notes
- You cannot use namespaced roles for non-namespaced resources (e.g. CustomResourceDefinitions)
- rbac.authorization.kubernetes.io/autoupdateannotation with a value of false to the built-in ClusterRole resource. If this annotation is set to false, the API server will notoverwrite the modified ClusterRole resource.
- to turn off system:unauthenticated users access to the API server’s API discovery endpoint, make sure --anonymous-auth=false flag is set on your API server.

6. Common Commands
- Testing Authorization with can-i
```bash
$ kubectl auth can-i create pods
$ kubectl auth can-i get pods --subresource=logs
```

- Reconcile
```bash
$ kubectl auth reconcile -f some-rbac-config.yaml (a command that operates somewhat like kubectl apply and will reconcile a text-based set of roles and role bindings with the current stateof the cluster.)
```

7. Aggregating ClusterRoles
This new role combines all of the capabilities of all of the aggregate roles together, and any changes to any of the constituent subroles will automatically be propogated back into the aggregate role.
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
	name: edit
...
aggregationRule:
	clusterRoleSelectors:
		- matchLabels:
            rbac.authorization.k8s.io/aggregate-to-edit: "true"
...
```

8. Using Groups for Bindings
When you bind a group to a ClusterRole or a namespace Role, anyone who is a member of that group gains access to the resources and verbs defined by that role.
- user
- group
   - can also bind user to a group
- role
   - extend|aggregate other roles
   - resource-groups
   - resources
   - actions (verbs)
- role-binding
   - bind user to a role
   - bind a group to a role


