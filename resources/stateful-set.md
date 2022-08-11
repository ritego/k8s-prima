# K8s

## Prima

### StatefulSet
1. Similar to Deployments

2. There are some vital differences between StatefulSets and Deployments, StatefulSets have:
- Predictable and persistent Pod names (e.g. database-0, database-1, etc.)
- Predictable and persistent DNS hostnames
- Predictable and persistent volume bindings

3. These three properties form the state of a Pod, sometimes referred to as its sticky ID.

4. StatefulSets ensure this state/sticky ID is persisted across failures, scaling, and other scheduling

5. Structure
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
	name: mongo
spec:
	selector:
		matchLabels:
			app: mongo
	serviceName: "tkb-sts"
	replicas: 3
	template:
		metadata:
			labels:
				app: mongo
		spec:
			containers:
				- name: ctr-mongo
					image: mongo:latest
```

6. Another fundamental characteristic of StatefulSets is the controlled and ordered way they start and stop Pods. StatefulSets create one Pod at a time, and always wait for previous Pods to be running and ready before creating the next. This is different from Deployments that use a ReplicaSet controller to start all Pods at the same time, causing potential race conditions.

7. Deleting a StatefulSet does not terminate Pods in order. With this in mind, you may want to scale aStatefulSet to 0 replicas before deleting it. 

8. Manual intervention is needed before Kubernetes will replace Pods on failed nodes.

9. Headless service
- A headless Service is just a regular Kubernetes Service object without an IP address (spec.clusterIP set to None).It becomes a StatefulSet’s governing Service when you list it in the StatefulSet manifest under spec.serviceName
```
apiVersion: v1
kind: Service
metadata:
	name: mongo
spec:
	ports:
		- port: 27017
			name: peer
	clusterIP: None #headless Service
	selector:
		app: mongo
```

- A headless service creates replica-specific DNS names that can be resolved just like service names <object-name>.<service-name>.<namespace>.svc.cluster.local

11. it’s possible to tweak the controlled and ordered starting and stopping of Pods via the StatefulSet’s spec.podManagementPolicy property.
- OrderedReady
- Parallel

