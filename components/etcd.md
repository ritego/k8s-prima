# K8s

## Prima

### ETCD
1. ETCD is a distributed reliable key-value store that is Simple, Secure & Fast
 
2.  Install
- Download
```bash
wget -q --https-only \ "https://github.com/coreos/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz"
```

- Setup
```bash
ExecStart=/usr/local/bin/etcd \\
--name ${ETCD_NAME} \\ --cert-file=/etc/etcd/kubernetes.pem \\ --key-file=/etc/etcd/kubernetes-key.pem \\ --peer-cert-file=/etc/etcd/kubernetes.pem \\ --peer-key-file=/etc/etcd/kubernetes-key.pem \\ --trusted-ca-file=/etc/etcd/ca.pem \\ --peer-trusted-ca-file=/etc/etcd/ca.pem \\ --peer-client-cert-auth \\
--client-cert-auth \\
--initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \\
--listen-peer-urls https://${INTERNAL_IP}:2380 \\
--listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \\
--advertise-client-urls https://${INTERNAL_IP}:2379 \\
--initial-cluster-token etcd-cluster-0 \\
--initial-cluster controller-0=https://${CONTROLLER0_IP}:2380,controller-1=https://${CONTROLLER1_IP}:2380 \\ --initial-cluster-state new \\
--data-dir=/var/lib/etcd
```

- Extract
```bash
$ tar xzvf etcd-v3.3.11-linux-amd64.tar.gz
```

- Run ETCD Service
```bash
$ ./etcd
```

#. Operate
```bash
$ ./etcd #Run ETCD Service 
$ ./etcdctl set key1 value1
$ ./etcdctl get key1
$ ./etcdctl

# run commands on cluster etcd
$ kubectl exec etcd-master –n kube-system etcdctl get / --prefix –keys-only 