# Getting Started
* Download CentOS Atomic Host http://cloud.centos.org/centos/7/atomic/images/
* Show version
```bash
# atomic host status
  TIMESTAMP (UTC)         VERSION        ID             OSNAME                 REFSPEC
* 2016-04-04 21:25:34     7.20160404     e39c28570a     centos-atomic-host     centos-atomic-host:centos-atomic-host/7/x86_64/standard
```
* Add proxy server for atomic host upgrade to /etc/ostree/remotes.d/centos-atomic-host.conf file.
```bash
[remote "centos-atomic-host"]
proxy=http://${PROXY_SERVER}:${PROXY_PORT}
```
* Upgrade Atomic Host
```bash
# setenforce 0
# atomic host upgrade
# atomic host status
State: idle
Deployments:
● centos-atomic-host:centos-atomic-host/7/x86_64/standard
       Version: 7.20170209 (2017-02-10 00:54:47)
        Commit: d433342b09673c9c4d75ff6eef50a447e73a7541491e5197e1dde14147b164b8
        OSName: centos-atomic-host
  GPGSignature: 1 signature
                Signature made Fri 10 Feb 2017 08:06:18 AM ICT using RSA key ID F17E745691BA8335
                Good signature from "CentOS Atomic SIG <security@centos.org>"

  centos-atomic-host:centos-atomic-host/7/x86_64/standard
       Version: 7.20160404 (2016-04-04 21:25:34)
        Commit: e39c28570a7ceb765feed1a0f017b94641e75137afd317730c7cd3a6651492f6
        OSName: centos-atomic-host
  GPGSignature: 1 signature
                Signature made Tue 05 Apr 2016 04:33:10 AM ICT using RSA key ID F17E745691BA8335
                Good signature from "CentOS Atomic SIG <security@centos.org>"
```
* Add proxy server for docker
```bash
# mkdir /etc/systemd/system/docker.service.d
# echo '[Service]' > /etc/systemd/system/docker.service.d/http-proxy.conf
# echo 'Environment="HTTP_PROXY=http://${PROXY_SERVER}:${PROXY_PORT}/"' >> /etc/systemd/system/docker.service.d/http-proxy.conf
# systemctl daemon-reload
# systemctl restart docker
```
* Search docker image from public registries
```bash
# docker search fedora
INDEX       NAME                                    DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/fedora                        Official Docker builds of Fedora                500       [OK]
docker.io   docker.io/mattsch/fedora-nzbhydra       Fedora NZBHydra                                 4                    [OK]
docker.io   docker.io/dockingbay/fedora-rust        Trusted build of Rust programming language...   3                    [OK]
docker.io   docker.io/startx/fedora                 Simple container used for all startx based...   3                    [OK]
docker.io   docker.io/darksheer/fedora              Hourly update latest Fedora Image               1                    [OK]
docker.io   docker.io/darksheer/fedora22            Base Fedora 22 Image -- Updated hourly          1                    [OK]
docker.io   docker.io/darksheer/fedora23            Hourly updated Fedora 23                        1                    [OK]
docker.io   docker.io/mattsch/fedora-rpmfusion      Base container for Fedora with RPM Fusion ...   1                    [OK]
docker.io   docker.io/pacur/fedora-22               Pacur Fedora 22                                 1                    [OK]
docker.io   docker.io/heichblatt/fedora-pkgsrc      pkgsrc on latest Fedora                         0                    [OK]
docker.io   docker.io/kino/fedora                   fedora base                                     0                    [OK]
docker.io   docker.io/krystalcode/fedora            Fedora base image that includes some addit...   0                    [OK]
docker.io   docker.io/mattsch/fedora-couchpotato    Fedora Couchpotato                              0                    [OK]
docker.io   docker.io/mattsch/fedora-htpc           Fedora HTPC                                     0                    [OK]
docker.io   docker.io/mattsch/fedora-nzbget         Fedora NZBGet                                   0                    [OK]
docker.io   docker.io/mattsch/fedora-plex           Fedora Plex                                     0                    [OK]
docker.io   docker.io/mattsch/fedora-sonarr         Fedora Sonarr                                   0                    [OK]
docker.io   docker.io/mattsch/fedora-transmission   Fedora Transmission                             0                    [OK]
docker.io   docker.io/opencpu/fedora                Stable version of opencpu for Fedora            0                    [OK]
docker.io   docker.io/qnib/fedora                   Base QNIBTerminal image of fedora               0                    [OK]
docker.io   docker.io/smartentry/fedora             fedora with smartentry                          0                    [OK]
docker.io   docker.io/termbox/fedora                Fedora                                          0                    [OK]
docker.io   docker.io/vcatechnology/fedora          A Fedora image that is updated daily            0                    [OK]
docker.io   docker.io/vcatechnology/fedora-ci       A Fedora image that is used in the VCA Tec...   0                    [OK]
docker.io   docker.io/zartsoft/fedora               Base fedora image                               0                    [OK]
```
