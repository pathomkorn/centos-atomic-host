# Setup Kubernetes
## Setup Kubernetes Master
* Install new CentOS 7 server
* Disable firewalld and selinux
* Install Kubernetes components
```bash
# yum install -y etcd kubernetes docker
# vi /etc/etcd/etcd.conf
ETCD_LISTEN_CLIENT_URLS="http://0.0.0.0:2379,http://0.0.0.0:4001"
ETCD_ADVERTISE_CLIENT_URLS="http://0.0.0.0:2379,http://0.0.0.0:4001"
# vi /etc/kubernetes/config
KUBE_LOGTOSTDERR="--logtostderr=true"
KUBE_LOG_LEVEL="--v=0"
KUBE_ALLOW_PRIV="--allow-privileged=false"
KUBE_MASTER="--master=http://${KUBE_MASTER_FQDN}:8080"
# vi /etc/kubernetes/controller-manager
KUBELET_ADDRESSES="--machines=http://${KUBE_NODE1_FQDN}:8080,http://${KUBE_NODE2_FQDN}:8080"
KUBE_CONTROLLER_MANAGER_ARGS=""
# vi /etc/kubernetes/apiserver
KUBE_API_ADDRESS="--address=0.0.0.0"
KUBE_API_PORT="--port=8080"
KUBELET_PORT="--kubelet-port=10250"
KUBE_ETCD_SERVERS="--etcd-servers=http://${KUBE_MASTER_FQDN}:2379"
KUBE_SERVICE_ADDRESSES="--service-cluster-ip-range=10.254.0.0/16"
KUBE_API_ARGS=""
# systemctl restart etcd kube-apiserver kube-controller-manager kube-scheduler
# systemctl enable etcd kube-apiserver kube-controller-manager kube-scheduler
# systemctl status etcd kube-apiserver kube-controller-manager kube-scheduler
```

## Setup CentOS Atomic Host as Kubernetes Nodes
```bash
# vi /etc/kubernetes/config
KUBE_LOGTOSTDERR="--logtostderr=true"
KUBE_LOG_LEVEL="--v=0"
KUBE_ALLOW_PRIV="--allow-privileged=false"
KUBE_MASTER="--master=http://${KUBE_MASTER_FQDN}:8080"
# vi /etc/kubernetes/proxy
KUBE_PROXY_ARGS="--master=http://${KUBE_MASTER_FQDN}:8080"
# vi /etc/kubernetes/kubelet
KUBELET_ADDRESS="--address=0.0.0.0"
KUBELET_PORT="--port=10250"
KUBELET_HOSTNAME="--hostname-override=${KUBE_NODE_FQDN}"
KUBELET_API_SERVER="--api-servers=http://${KUBE_MASTER_FQDN}:8080"
KUBELET_POD_INFRA_CONTAINER="--pod-infra-container-image=registry.access.redhat.com/rhel7/pod-infrastructure:latest"
KUBELET_ARGS="--auth_path=/var/lib/kubelet/auth"
# echo "{}" > /var/lib/kubelet/auth
# systemctl restart docker kube-proxy kubelet
# systemctl enable docker kube-proxy kubelet
# systemctl status docker kube-proxy kubelet
```

## Verification on Kubernetes Master
```bash
#curl -L http://127.0.0.1:2379/version
{"etcdserver":"3.0.15","etcdcluster":"3.0.0"}
# curl -s -L http://127.0.0.1:8080/version
{
  "major": "1",
  "minor": "4",
  "gitVersion": "v1.4.0",
  "gitCommit": "87d9d8d7bc5aa35041a8ddfe3d4b367381112f89",
  "gitTreeState": "clean",
  "buildDate": "2017-01-23T14:15:25Z",
  "goVersion": "go1.6.3",
  "compiler": "gc",
  "platform": "linux/amd64"
}
# kubectl get endpoints
NAME         ENDPOINTS             AGE
${MASTER}    ${MASTER_IP}:6443     28m
# kubectl get nodes
NAME                  STATUS    AGE
${KUBE_NODE_FQDN}     Ready     8m
```
