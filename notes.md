# K8s

## Prima

### Concepts
- Root privileges can be obtained by running 'sudo −i'.
- ssh <nodename>
- Copy  = Ctrl+SHIFT+C (inside the terminal)
- Paste = Ctrl+SHIFT+V (inside the terminal)
- OR Use the Right Click Context Menu and select Copy or Paste

## Key directories
- /etc/systemd/system/
- /var/log/
- /var/log/pods
- /var/log/containers
- /etc/kurbenetes/manifest
- /etc/cni/net.d

## Key Commands
- ps -aux | grep kube-apiserver
- crictl ps + crictl logs
- docker ps + docker logs (in case when Docker is used)
- kubelet logs: /var/log/syslog or journalctl
-  echo –n ‘mysql’ | base64
- echo –n ‘bXlzcWw=’ | base64 --decode
- service kube-apiserver status
- sudo journalctl -u kube-apiserver
- openssl x509 -in /var/lib/kubelet/worker-1.crt -text
- k get componentstatus
- k cluster-info
- k config view
- sudo tar -zcvf etcd.tar.gz /etc/kubernetes/pki/etcd
- scp etcd.tar.gz cloud_user@18.219.235.42:~/
- ifconfig
- service kubelet start
- whereis kubelet
- systemctl daemon-reload && systemctl restart kubelet
- cat /etc/resolv.conf
- apt show kubectl -a
- kubeadm token create --print-join-command
- openssl x509  -noout -text -in /etc/kubernetes/pki/apiserver.crt o *.pem
- kubeadm certs check-expiration
- kubeadm certs renew apiserver
-   -o jsonpath="{range .items[*]} {.metadata.name}{.spec.containers[*].resources}{'\n'}"
- ssh cluster1-master1 iptables-save | grep p2-service
- k get pod,svc -l run=check-ip

` cat <<EOF | kubectl create -f -
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: pod-csr.web
spec:
  groups:
  - system:authenticated
  request: $(cat server.csr | base64 | tr -d '\n')
  usages:
  - digital signature
  - key encipherment
  - server auth
EOF