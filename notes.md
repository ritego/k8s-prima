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