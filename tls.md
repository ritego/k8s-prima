# K8s

## Prima

### TLS Certificates
1. Introduction
- Private key
```
*.key *-key.pem
server.key server-key.pem 
client.key client-key.pem
```

- Public key (aka certificate)
```
*.crt *.pem
server.crt server.pem 
client.crt client.pem
```

2. Required certificates
- Api Server  
    - 1 pair of server cert
    - 1 pair of client for etcd 
    - 1 pair of client for kubelet
- ETCD
    - 1 pair of server cert
- Kubelet
    - 1 pair of server cert
    - 1 pair of client cert for api server
- Kube Controller
    - 1 pair of client cert for api server
- Kube Scheduler
    - 1 pair of client cert for api server
- Kube Proxy
    - 1 pair of client cert for api server

3. Generate Certs
- Means
    - EasyRSA
    - OpenSSL
    - CFSSL

- 
- CA `-subj "/CN=KUBERNETES-CA"`
- Admin User `"/CN=kube-admin/OU=system:master"`
- Scheduler `"/CN=kube-admin/OU=system:kube-scheduler"`
- Controller `"/CN=kube-admin/OU=system:kube-controller-manager"`
- Proxy `"/CN=kube-admin/OU=system:kube-proxy"`
- Admin User `"/CN=kube-admin/OU=etcd-server"`
- Admin User `"/CN=kube-admin/OU=etcd-peer"`
- Admin User `"/CN=kube-admin/OU=kube-apiserver"`

```bash
# generate key
$ openssl genrsa -out my_key.key 2048 

# generate cert
$ openssl req -new -key my_key.key -subj "/CN=kube-admin/OU=system:master" -out my_cert.csr

# sign cert
$ openssl x509 -req -in my_cert.csr  -signkey my_key.key -out my_cert.crt
OR
$ openssl x509 -req -in my_cert.csr –CA ca.crt -CAkey ca.key -out my_cert.crt

# check cert
$ openssl x509  -noout -text -in /etc/kubernetes/pki/apiserver.crt


# For User
$ openssl genrsa -out jane.key 2048
$ openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr

# OR
$ k create csr jane
$ k get csr
$ k certificate approve jane
```

```bash
$ kubeadm certs check-expiration # check expiration
$ kubeadm certs renew apiserver #renew cert for apiserver
```

```bash
# check localtion of certs
$ cat /etc/systemd/system/*.service
$ cat /etc/kubernetes/manifests/*.yaml
```