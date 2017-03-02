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
    run: httpd
spec:
  containers:
    - name: httpd-c1
      image: httpd
      ports:
        - containerPort: 80
# kubectl create -f httpd-pod.yml
pod "httpd-pod" created
# kubectl get pods -o wide
NAME        READY     STATUS    RESTARTS   AGE       IP           NODE
httpd-pod   1/1       Running   0          3m        172.17.0.2   ${KUBE_NODE}
# kubectl describe pods httpd-pod
Name:           httpd-pod
Namespace:      default
Node:           ${KUBE_NODE_FQDN}/${KUBE_NODE_IP}
Start Time:     Thu, 02 Mar 2017 11:32:44 +0700
Labels:         run=httpd
Status:         Running
IP:             172.17.0.2
Controllers:    <none>
Containers:
  httpd-c1:
    Container ID:               docker://ff1107526ac6a436905e1f4f230c8b490c013fc6ec33f146496304174baa9d6a
    Image:                      httpd
    Image ID:                   docker-pullable://docker.io/httpd@sha256:4612fba4347bd87eaeecd5c522d844f26cc4065b45eef9291277497946b7a86c
    Port:                       80/TCP
    State:                      Running
      Started:                  Thu, 02 Mar 2017 11:32:49 +0700
    Ready:                      True
    Restart Count:              0
    Volume Mounts:              <none>
    Environment Variables:      <none>
Conditions:
  Type          Status
  Initialized   True
  Ready         True
  PodScheduled  True
No volumes.
QoS Class:      BestEffort
Tolerations:    <none>
# kubectl get pods -l run=httpd -o yaml | grep podIP
    podIP: 172.17.0.2
# docker ps
CONTAINER ID        IMAGE                                                        COMMAND              CREATED             STATUS              PORTS               NAMES
ff1107526ac6        httpd                                                        "httpd-foreground"   2 minutes ago       Up 2 minutes                            k8s_httpd-c1.34d1fe1a_httpd-pod_default_47aadb85-ff01-11e6-85eb-00505681075c_eedbbdc8
4aa050358220        registry.access.redhat.com/rhel7/pod-infrastructure:latest   "/pod"               2 minutes ago       Up 2 minutes                            k8s_POD.a8590b41_httpd-pod_default_47aadb85-ff01-11e6-85eb-00505681075c_70db5956
```

## Create service
```bash
# cat httpd-service.yml
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
  labels:
    run: httpd
spec:
  selector:
    run: httpd
  ports:
    - port: 8080
      targetPort: 80
      protocol: TCP
  externalIPs:
    - 192.168.111.56
# kubectl create -f httpd-service.yml
service "httpd-service" created
# kubectl get services -o wide
NAME            CLUSTER-IP      EXTERNAL-IP      PORT(S)    AGE       SELECTOR
httpd-service   10.254.25.140   192.168.111.56   8080/TCP   15s       run=httpd
kubernetes      10.254.0.1      <none>           443/TCP    56s       <none>
# kubectl describe service httpd-service
Name:                   httpd-service
Namespace:              default
Labels:                 run=httpd
Selector:               run=httpd
Type:                   ClusterIP
IP:                     10.254.25.140
External IPs:           192.168.111.56
Port:                   <unset> 8080/TCP
Endpoints:              172.17.0.2:80
Session Affinity:       None
No events.
# kubectl get endpoints httpd-service
NAME            ENDPOINTS       AGE
httpd-service   172.17.0.2:80   1m
```

## Verification
```bash
# curl http://${externalIPs}:8080
<html><body><h1>It works!</h1></body></html>
```

## Terminate pod and service
```bash
# kubectl delete pods --all
pod "httpd-pod" deleted
# kubectl delete services --all
service "httpd-service" deleted
service "kubernetes" deleted
```
