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
KUBE_ADMISSION_CONTROL="--admission-control=NamespaceLifecycle,NamespaceExists,LimitRanger,SecurityContextDeny,ServiceAccount,ResourceQuota"
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

## Create pod
```bash
# vi httpd-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
  labels:
    name: httpd-pod
spec:
  containers:
    - name: centos1
      image: httpd
      ports:
        - containerPort: 80
          hostPort: 8080
          name: http
# kubectl create -f httpd-pod.yml
pod "httpd-pod" created
# kubectl get pods
NAME        READY     STATUS              RESTARTS   AGE
httpd-pod   0/1       ContainerCreating   0          32s
# kubectl get pods
NAME        READY     STATUS    RESTARTS   AGE
httpd-pod   1/1       Running   0          1m
# kubectl describe pods httpd-pod
Name:           httpd-pod
Namespace:      default
Node:           ${KUBE_NODE_FQDN}/${KUBE_NODE_IP}
Start Time:     Thu, 02 Mar 2017 01:46:27 +0700
Labels:         <none>
Status:         Running
IP:             172.17.0.2
Controllers:    <none>
Containers:
  centos1:
    Container ID:               docker://ac377f85cf54dc2a5fa9eaabde0f2448e934068dd061080e15bc5525b31f6c03
    Image:                      httpd
    Image ID:                   docker-pullable://docker.io/httpd@sha256:81fa70287ee1d56fb9156f6f89137363c50c40dd088a46568f4c2f7a901e2674
    Port:                       80/TCP
    State:                      Running
      Started:                  Thu, 02 Mar 2017 01:46:32 +0700
    Ready:                      True
    Restart Count:              0
    Volume Mounts:              <none>
    Environment Variables:      <none>
# docker ps
CONTAINER ID        IMAGE                                                        COMMAND              CREATED              STATUS              PORTS                  NAMES
ac377f85cf54        httpd                                                        "httpd-foreground"   About a minute ago   Up About a minute                          k8s_centos1.70d00061_httpd-pod_default_606beb74-feaf-11e6-85eb-00505681075c_829283c3
1ae854d7d8ac        registry.access.redhat.com/rhel7/pod-infrastructure:latest   "/pod"               About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp   k8s_POD.bb810da1_httpd-pod_default_606beb74-feaf-11e6-85eb-00505681075c_12860aee
```

## Create service
```bash
# vi httpd-service.yml
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
spec:
  selector:
    app: httpd-pod
  ports:
    - port: 8080
  type: LoadBalancer
# kubectl create -f httpd-service.yml
service "httpd-service" created
# kubectl get services
NAME            CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
httpd-service   10.254.22.69   <pending>     8080/TCP   1m
kubernetes      10.254.0.1     <none>        443/TCP    3m
[root@kubernetes ~]#
```

## Verification
```bash
# curl http://${KUBE_NODE_FQDN}:8080
<html><body><h1>It works!</h1></body></html>
# kubectl delete pods --all
pod "httpd-pod" deleted
# kubectl delete services --all
service "httpd-service" deleted
service "kubernetes" deleted
```
