# Getting Started
* Download CentOS Atomic Host
http://cloud.centos.org/centos/7/atomic/images/
* show atomic host version
```bash
# atomic host status
```
You'll need to add your proxy information to /etc/ostree/remotes.d/centos-atomic-host.conf

[remote "centos-atomic-host"]
proxy=http://your_proxy:3128


upgrade atomic host
# atomic host upgrade --> 2 version
# systemctl reboot
rollback atomic host
# atomic host rollback
