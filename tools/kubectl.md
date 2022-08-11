# K8s

## Prima

### Kubectl
1. At a high-level, kubectl converts user-friendly commands into HTTP REST requests with JSON content required by the API server. It uses a configuration file to know which cluster and API server endpoint to send commands to, and it takes care of sending authentication data with commands.

2. The kubectl configuration file is called config and lives in a hidden directory called kube in your home directory ($HOME/.kube/config)

3. the config file contains:
   - Clusters - definition has a name, certificate info, and API server endpoint.
   - Users(credentials) - define different users that might have different levels of access on each cluster
   - Contexts - group together clusters and users under a friendly name.
```yaml
apiVersion: v1
kind: Config
preferences: {}
clusters:
- cluster:
    certificate-authority-data: LS0t...LS0K
    server: https://192.168.64.3:6443
  name: default
- cluster:
    server: ""
  name: my-other-cluster
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
users:
- name: default
  user:
    client-certificate-data: LS0t...S0K
    client-key-data: LS0t...Qo=
```

4. Common Commands
```bash
$ kubectl 
- - config
- - explain
- - apply
- - get
- - describe
- - logs
- - exec -it
- - attach -it
- - create
- - get
- - run
- - expose
- - edit
- - delete
- - scale
- - rollout
- - api-resources
- - api-versions
- - expose
- - cluster-info
- - proxy
- - log -c (select pod) -f (follow,stream)
- - cp <pod-name>:</path/to/remote/file> </path/to/local/file>
- - port-forward <pod-name>|services/<service-name> 8080:80
- - top nodes|pods
- - help <command-name>
```
[...more](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-)

5. Config Management Commands
```bash
$ delete|get|set|use|current cluster|user|context(s)
```

6. Activate command line autocomplete
- https://kubernetes.io/docs/reference/kubectl/cheatsheet/#bash
