# K8s

## Prima

### Storage
1. Objects
- Volume
- PersistentVolume
- PersistentVolumeClaim
- Volume

### Volume
1. On-disk files in a container are ephemeral, which presents some problems for non-trivial applications when running in containers. 
    - the loss of files when a container crashes.
    - sharing files between containers running together in a Pod is difficult since every container has an isolated filesystem. 
2. The Kubernetes volume abstraction solves both of above problems.

3. At its core, a volume is a directory, possibly with some data in it, which is accessible to the containers in a pod. How that directory comes to be, the medium that backs it, and the contents of it are determined by the particular volume type used.

4. Subpath
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-lamp-site
spec:
    containers:
    - name: mysql
      image: mysql
      env:
      - name: MYSQL_ROOT_PASSWORD
        value: "rootpasswd"
      volumeMounts:
      - mountPath: /var/lib/mysql
        name: site-data
        subPath: mysql # my-lamp-site-data/mysql
    - name: php
      image: php:7.0-apache
      volumeMounts:
      - mountPath: /var/www/html
        name: site-data
        subPath: html # my-lamp-site-data/html
    volumes:
    - name: site-data
      persistentVolumeClaim:
        claimName: my-lamp-site-data
```

5. Types
- emptyDir - An emptyDir volume is first created when a Pod is assigned to a node, and exists as long as that Pod is running on that node. As the name says, the emptyDir volume is initially empty. All containers in the Pod can read and write the same files in the emptyDir volume, though that volume can be mounted at the same or different paths in each container. When a Pod is removed from a node for any reason, the data in the emptyDir is deleted permanently. The data in an emptyDir volume is safe across container crashes.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir: {
        medium: "Memory" # to mounts a tmpfs (RAM-backed filesystem)
    }
```

- hostPath - A hostPath volume mounts a file or directory from the host node's filesystem into your Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /data
      type: Directory | DirectoryOrCreate | File | FileOrCreate | Socket | CharDevice | BlockDevice
```

- configMap -  inject configuration data into pods
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-pod
spec:
  containers:
    - name: test
      image: busybox:1.28
      volumeMounts:
        - name: config-vol
          mountPath: /etc/config
  volumes:
    - name: config-vol
      configMap:
        name: log-config
        items:
          - key: log_level
            path: log_level
```

- secret - A secret volume is used to pass sensitive information, such as passwords, to Pods. You can store secrets in the Kubernetes API and mount them as files for use by pods without coupling to Kubernetes directly. secret volumes are backed by tmpfs (a RAM-backed filesystem) so they are never written to non-volatile storage.
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
```

- PersistentVolume
- local - A local volume represents a mounted local storage device such as a disk, partition or directory.
- downwardAPI
- nfs
- fc (fibre channel)
- glusterfs
- cephfs 
- iscsi
- portworxVolume
- projected - A projected volume maps several existing volume sources into the same directory.
- rbd

6. CSI
Container Storage Interface (CSI) defines a standard interface for container orchestration systems (like Kubernetes) to expose arbitrary storage systems to their container workloads.

Once a CSI compatible volume driver is deployed on a Kubernetes cluster, users may use the csi volume type to attach or mount the volumes exposed by the CSI driver.

A csi volume can be used in a Pod in three different ways:

    through a reference to a PersistentVolumeClaim
    with a generic ephemeral volume
    with a CSI ephemeral volume if the driver supports that (beta feature)

### PersistentVolume
1. The PersistentVolume subsystem provides an API for users and administrators that abstracts details of how storage is provided from how it is consumed. 

2. PVs are volume plugins like Volumes, but have a lifecycle independent of any individual Pod that uses the PV

3. Provisioning
- Static - A cluster administrator creates the PVs. They exist in the Kubernetes API and are available for consumption.
- Dynamic - The PV is created automatically based on PVC matching a PV. The PV is created based on the defined storage class

### PersistentVolumeClaim
1. A PersistentVolumeClaim (PVC) is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany or ReadWriteMany)

2. Matching claims with volumes is controlled by:
- Labels (PV must exist already)
```yaml
selector: 
  matchLabels:
    name: my-pv
```

- Sufficient Capacity
```yaml
spec:
  accessModes:
     - ReadWriteOnce
  resources:
     requests:
       storage: 500Mi
```

- Access Modes
```yaml
spec:
  accessModes:
     - ReadWriteOnce
```

- Volume Modes
```yaml
spec:
  accessModes:
     - ReadWriteOnce
  resources:
     requests:
       storage: 500Mi
```

- Storage Class
```yaml
```

1. Create a multi-container pod and have the pod’s containers operate on thesame files by adding a volume to the pod and mounting it in each container
 Use the emptyDir volume to store temporary, non-persistent data
 Use the gitRepo volume to easily populate a directory with the contents of a Gitrepository at pod startup
 Use the hostPath volume to access files from the host node
 Mount external storage in a volume to persist pod data across pod restarts
 Decouple the pod from the storage infrastructure by using PersistentVolumesand PersistentVolumeClaims
 Have PersistentVolumes of the desired (or the default) storage class dynamicallyprovisioned for each 

PersistentVolumeClaim
 Prevent the dynamic provisioner from interfering when you want the Persistent-VolumeClaim to be bound to a pre-provisioned PersistentVolume

Kubernetes has a mature and featurerichstorage subsystem called the persistent volume subsystem.

no matter what type of storage, or where it comes from, when it’s exposed on Kubernetes it’s called a volume.

Modern plugins are be based on the Container Storage Interface (CSI) which is an openstandard aimed at providing a clean storage interface for container orchestrators such as Kubernetes. If you’rea developer writing storage plugins, the CSI abstracts the internal Kubernetes machinery and lets you developout-of-tree.

There are a growing number of storage-related API objects, but the core onesare:
• Persistent Volumes (PV)
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: database
  labels:
    volume: my-volume
spec:
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 1Gi
  nfs:
    server: 192.168.0.1
    path: "/exports"
```

• Persistent Volume Claims (PVC)
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: database
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  selector:
    matchLabels:
      volume: my-volume
```
• Storage Classes (SC) (dynamic storage provision)
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: default
  annotations:
    storageclass.beta.kubernetes.io/is-default-class: "true"
  labels:
    kubernetes.io/cluster-service: "true"
provisioner: kubernetes.io/azure-disk
```

At a high level, PVs are how external storage assets are represented in Kubernetes. PVCs are like tickets thatgrant a Pod access to a PV. SCs make it all dynamic.

You cannot map an external storage volume to multiple PVs. For example, you cannot have a 50GB externalvolume that has two 25GB Kubernetes PVs each using half of it.
Sometimes we call plugins “provisioners”, especially when we talk about Storage Classes

storage classes are resources in the storage.k8s.io/v1 API group.

Kubernetes supports three access modes for volumes.
• ReadWriteOnce (RWO)
• ReadWriteMany (RWM)
• ReadOnlyMany (ROM)

Importing External Services
```
kind: Service
apiVersion: v1
metadata:
	name: external-database
spec:
	type: ExternalName
	externalName: database.company.com

OR

kind: Service
apiVersion: v1
metadata:
	name: external-ip-database

---

kind: Endpoints
apiVersion: v1
metadata:
	name: external-ip-database
subsets:
	- addresses:
		- ip: 192.168.0.1
			ports:
				- port: 3306
```
* When a typical Kubernetes service is created, an IP address is also createdand the Kubernetes DNS service is populated with an A record that pointsto that IP address. When you create a service of type ExternalName,the Kubernetes DNS service is instead populated with a CNAME recordthat points to the external name you specified


Running Reliable Singletons
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: database
  labels:
    volume: my-volume
spec:
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 1Gi
  nfs:
    server: 192.168.0.1
    path: "/exports"

----

kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: database
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  selector:
    matchLabels:
      volume: my-volume
---

apiVersion: extensions/v1
kind: ReplicaSet
metadata:
  name: mysql
  labels:
    app: mysql
spec:
	replicas: 1
	...
  template:
    spec:
      containers:
        volumeMounts:
          - name: database
            mountPath: "/var/lib/mysql"
      volumes:
        - name: database
          persistentVolumeClaim:
            claimName: database

---

apiVersion: v1
kind: Service
metadata:
	name: mysql
	spec:
		ports:
			- port: 3306
				protocol: TCP
		selector:
			app: mysql
```
