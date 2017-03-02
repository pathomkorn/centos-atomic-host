# Setup Flannel
## Setup Flanneld On Kubernetes Master
```bash
# vi flannel-config.json
{
  "Network": "10.20.0.0/16",
  "SubnetLen": 24,
  "SubnetMin": "10.20.50.0",
  "SubnetMax": "10.20.199.0",
  "Backend": {
    "Type": "vxlan",
    "VNI": 1
  }
}
# etcdctl set atomic.io/network/config < flannel-config.json
# etcdctl get /atomic.io/network/config
# yum install -y flannel
```
## Setup Flanneld On Kubernetes Master and Nodes
```bash
# vi /etc/sysconfig/flanneld
FLANNEL_ETCD_ENDPOINTS="http://${KUBE_MASTER_FQDN}:2379"
FLANNEL_ETCD_PREFIX="/atomic.io/network"
FLANNEL_OPTIONS="-iface=ens192"
# systemctl restart flanneld
# systemctl enable flanneld
# systemctl status flanneld
# ip link show flannel.1
4: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN mode DEFAULT
    link/ether 3e:52:29:95:e4:40 brd ff:ff:ff:ff:ff:ff
```
