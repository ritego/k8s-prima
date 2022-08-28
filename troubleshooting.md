# K8s

## Prima

### Troubleshooting
#### Apps
1. Debugging Pods
- Pending State
    - the pod could not be schedule
    - check status n events: `kubectl describe pods ${POD_NAME}`
    - could be due to insufficient resource (memory, cpu) on the cluster
    - or use of hostPort. with hostport you can only schedule as many Pods as there are nodes in your cluster.

- Waiting State
    - pod has been scheduled, but it can't run on the worker node
    - check status n events: `kubectl describe pods ${POD_NAME}`
    - check image name

- Crashing Pod
- Pod is running, but misbehaving
    - compare running yaml file with original intention: `kubectl get pods ${POD_NAME} -o yaml`

- (Init) Container
    - check logs `kubectl logs <pod-name> -c <init-container-2>`

- Connect terminal to pod `kubectl exec -it shell-demo -- sh` | `kubectl exec shell-demo -c ctn --it -- /bin/bash`
- 
2. Debugging Replication Controllers
- Replication controllers are fairly straightforward. They can either create Pods or they can't.
- You can also use `kubectl describe rc ${CONTROLLER_NAME}` to introspect events related to the replication controller.

3. Service (check pods, endpoints, dns, kube-proxy)
- does the service exist? `kubectl get svc svc_name`
- Any Network Policy Ingress rules affecting the target Pods? `kubectl get netpol`
- Does the Service work by DNS name? `nslookup svc_name[.default.svc.cluster.local]` from a pod
- check resolv config `cat /etc/resolv.conf` inside pod
- Is the Service port you are trying to access listed in spec.ports[]?
- Is the targetPort correct for your Pods (some Pods use a different port than the Service)?
- If you meant to use a numeric port, is it a number (9376) or a string "9376"?
- If you meant to use a named port, do your Pods expose a port with the same name?
- Is the port's protocol correct for your Pods?
- check if endpoint is missing
    - check endpoints `kubectl get endpoints ${SERVICE_NAME}`
    - compare to pods in scope `kubectl get pods --selector=name=nginx,type=frontend`
- also Verify that the pod's containerPort matches up with the Service's targetPort
- if network traffic is not forwarded
- is kube-proxy working?

4. Kube Proxy
- is it running? `ps aux | grep kube-proxy`
- is it failing? `journalctl`
- check iptables? `iptables-save | grep hostnames`
- check ipvs? `ipvsadm -ln`
- check userspace mode? `iptables-save | grep hostnames`

5. Statefulset
- list pods `kubectl get pods -l key=value`
- check pods with Unknown or Terminating state 

#### Cluster
- verify that all of the nodes you expect to see are present and that they are all in the Ready state
```bash
$ kubectl get nodes
```

- get detailed information about the overall health of your cluste
```bash
$ kubectl cluster-info dump
```

- check node logs
```bash
$ journalctl
$ ls /var/log/kube* e.g
```

- check metrics `kubectl top`

- Debugging Kubernetes nodes with crictl
```bash
$ crictl pods|images|ps
$ crictl pods --name nginx-65899c769f-wv2gp
$ crictl images nginx
$ crictl ps (-a)
$ crictl exec -i -t 1f73f2d81bf98 ls
$ crictl logs 87d3992f84f74
$ crictl logs --tail=1 87d3992f84f74 # latest N lines
```
- Tools
    - Prometheus
    - Node Problem Detector (+ Kernel Monitor) Daemonset 