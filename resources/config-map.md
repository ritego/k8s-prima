# K8s

## Prima

### ConfigMaps
1. Kubernetes provides an object called a ConfigMap (CM) that lets you store configuration data outside of a Pod. It also lets you dynamically inject the config into a Pod at run-time. A ConfigMap is an API object used to store non-confidential data in key-value pairs. It does not provide secrecy or encryption

2. ConfigMaps are used to provide configuration information for workloads. This can either be fine-grained information (a short string) or a composite value in the form of a file.

3. Structure
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
	name: mongo-init
data:
    # property-like keys; each key maps to a simple value
    player_initial_lives: "3"
    ui_properties_file_name: "user-interface.properties"

    # file-like keys
    game.properties: |
        enemy.types=aliens,monsters
        player.maximum-lives=5    
    user-interface.properties: |
        color.good=purple
        color.bad=yellow
        allow.textmode=true 

# Once a ConfigMap is marked as immutable, it is not possible to revert this change nor to mutate the contents of the data or the binaryData field. You can only delete and recreate the ConfigMap. Because existing Pods maintain a mount point to the deleted ConfigMap, it is recommended to recreate these pods.
immutable: true 
```

4. There are three main ways to use a ConfigMap:
- Inside a container command and args (set as end, the use env in command or arg)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/echo", "$(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
      env:
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_LEVEL
        - name: SPECIAL_TYPE_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_TYPE
  restartPolicy: Never
```
- Environment variables for a container
```yaml
kind: Pod
spec:
    containers:
        - image: some/image
            env:
                - name: FIRST_VAR
                  valueFrom:
                    configMapKeyRef:
                        name: fortune-config
                        key: sleep-interval
---
kind: Pod
spec:
    containers:
        - image: some/image
            envFrom:
                - prefix: CONFIG_
                    configMapRef:
                        name: my-config-map
```

- Add a file in read-only volume, for the application to read
```yaml
kind: Pod
spec:
    containers:
        - image: some/image
          volumeMounts:
            - name: config
                mountPath: "/config"
                readOnly: true
 volumes:
    - name: config
      configMap:
        name: game-demo
        items:
        - key: "game.properties"
          path: "game.properties"
        - key: "user-interface.properties"
          path: "user-interface.properties"
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    configMap:
      name: myconfigmap
```

- Write code to run inside the Pod that uses the Kubernetes API to read a ConfigMap
```yaml

```

#### Secrets
1. A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key.

2. Secrets are similar to ConfigMaps but focused on makingcsensitive information available to the workload. They can be used for things like credentials or TLS certificates.

3. Structure
```yaml
apiVersion: v1
kind: Secret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: { ... }
  name: mysecret
type: Opaque
data:
    #  The values are Base64 strings in the manifest; however, when you use the Secret with a Pod then the kubelet provides the decoded data to the Pod and its containers.
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm

# Use to keep values as plain
stringData: 
  username: plain
  password: plain

immutable: true
```

4. There are three main ways for a Pod to use a Secret:
- As files in a volume mounted on one or more of its containers
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
      defaultMode: 0400 # permission
      optional: false # default setting; "mysecret" must exist
    ---
    # You can also control the paths within the volume where Secret keys are projected
  volumes:
  - name: foo
    secret:
      secretName: mysecret
      items:
      - key: username
        path: my-group/my-username
        defaultMode: 0400 # permission
```
- As container environment variable
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
            optional: false # same as default; "mysecret" must exist
                            # and include a key named "username"
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
            optional: false # same as default; "mysecret" must exist
                            # and include a key named "password"
  restartPolicy: Never

---

kind: Pod
spec:
    containers:
        - image: some/image
            envFrom:
                secretRef:
                    name: my-config-map
```

-
6. Specific Use Case / Types
- Private Docker Registries - If you are repeatedly pulling from the same registry, you can add the secrets to the default service account associated with each Pod to avoid having to specify the secrets in every Pod you create
```yaml
kind: Pod
spec:
 imagePullSecrets:
		- name: my-image-pull-secret
```

- dotfiles in a secret volume
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: dotfile-secret
data:
  .secret-file: dmFsdWUtMg0KDQo=
```

- Service account token Secrets
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-sa-sample
  annotations:
    kubernetes.io/service-account.name: "sa-name"
type: kubernetes.io/service-account-token
data:
  extra: YmFyCg==
```

- Docker config Secrets
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-dockercfg
type: kubernetes.io/dockercfg
data:
  .dockercfg: |
        "<base64 encoded ~/.dockercfg file>"

apiVersion: v1
kind: Secret
metadata:
  name: secret-dockercfg
type: kubernetes.io/dockercfg
data:
  .dockerconfigjson: |
        "<base64 encoded ~/.docker/config.json file>"
```

- Basic Auth 
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-basic-auth
type: kubernetes.io/basic-auth
stringData:
  username: admin      # required field for kubernetes.io/basic-auth
  password: t0p-Secret # required field for kubernetes.io/basic-auth
```

- SSH authentication secrets
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-ssh-auth
type: kubernetes.io/ssh-auth
data:
  # the data is abbreviated in this example
  ssh-privatekey: |
          MIIEpQIBAAKCAQEAulqb/Y ...
```

- TLS secrets
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: secret-tls
type: kubernetes.io/tls
data:
  # the data is abbreviated in this example
  tls.crt: |
        MIIC2DCCAcCgAwIBAgIBATANBgkqh ...
  tls.key: |
        MIIEpgIBAAKCAQEA7yn3bRHQ5FHMQ ...
```

- Node Bootstrap token Secrets
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: bootstrap-token-5emitj
  namespace: kube-system
type: bootstrap.kubernetes.io/token
data:
  auth-extra-groups: c3lzdGVtOmJvb3RzdHJhcHBlcnM6a3ViZWFkbTpkZWZhdWx0LW5vZGUtdG9rZW4=
  expiration: MjAyMC0wOS0xM1QwNDozOToxMFo=
  token-id: NWVtaXRq
  token-secret: a3E0Z2lodnN6emduMXAwcg==
  usage-bootstrap-authentication: dHJ1ZQ==
  usage-bootstrap-signing: dHJ1ZQ==
```

7. In order to safely use Secrets, take at least the following steps:
- Enable Encryption at Rest for Secrets.
- Enable or configure RBAC rules that restrict reading and writing the Secret. Be aware that secrets can be obtained implicitly by anyone with the permission to create a Pod.
- Where appropriate, also use mechanisms such as RBAC to limit which principals are allowed to create new Secrets or replace existing ones.

