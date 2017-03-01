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
* Show docker verion
```bash
# docker version
Client:
 Version:         1.12.5
 API version:     1.24
 Package version: docker-common-1.12.5-14.el7.centos.x86_64
 Go version:      go1.7.4
 Git commit:      047e51b/1.12.5
 Built:           Mon Jan 23 15:35:13 2017
 OS/Arch:         linux/amd64

Server:
 Version:         1.12.5
 API version:     1.24
 Package version: docker-common-1.12.5-14.el7.centos.x86_64
 Go version:      go1.7.4
 Git commit:      047e51b/1.12.5
 Built:           Mon Jan 23 15:35:13 2017
 OS/Arch:         linux/amd64
```
* Add proxy server for docker
```bash
# mkdir /etc/systemd/system/docker.service.d
# echo '[Service]' > /etc/systemd/system/docker.service.d/http-proxy.conf
# echo 'Environment="HTTP_PROXY=http://${PROXY_SERVER}:${PROXY_PORT}/"' >> /etc/systemd/system/docker.service.d/http-proxy.conf
# systemctl daemon-reload
# systemctl restart docker
```
* Search fedora docker image from public registries with 4 stars
```bash
# docker search -s 4 fedora
Flag --stars has been deprecated, use --filter=stars=3 instead
INDEX       NAME                                DESCRIPTION                        STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/fedora                    Official Docker builds of Fedora   500       [OK]
docker.io   docker.io/mattsch/fedora-nzbhydra   Fedora NZBHydra                    4                    [OK]
```
* Download fedora docker image
```bash
# docker pull fedora
Using default tag: latest
Trying to pull repository docker.io/library/fedora ...
latest: Pulling from docker.io/library/fedora
1b39978eabd9: Pull complete
Digest: sha256:8d3f642aa4d3fa8f9dc52ab0e3bbbe8bc2494843dc6ebb26c4a6958db888e5a2
```
* Show local docker images
```bash
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/fedora    latest              1f8ec1108a3f        12 days ago         230.3 MB
# cat /var/lib/docker/image/devicemapper/repositories.json
{"Repositories":{"docker.io/fedora":{"docker.io/fedora:latest":"sha256:1f8ec1108a3fd027acd5d0bcc574afc179d15e9469b887e015d92116b853eed8","docker.io/fedora@sha256:8d3f642aa4d3fa8f9dc52ab0e3bbbe8bc2494843dc6ebb26c4a6958db888e5a2":"sha256:1f8ec1108a3fd027acd5d0bcc574afc179d15e9469b887e015d92116b853eed8"}}}
```
* Tag images
```bash
# docker tag 1f8ec1108a3f fedora/2017-03-01
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/fedora    latest              1f8ec1108a3f        12 days ago         230.3 MB
fedora/2017-03-01   latest              1f8ec1108a3f        12 days ago         230.3 MB
```
* Remove local docker images
```bash
# docker rmi docker.io/fedora
Untagged: docker.io/fedora:latest
Untagged: docker.io/fedora@sha256:8d3f642aa4d3fa8f9dc52ab0e3bbbe8bc2494843dc6ebb26c4a6958db888e5a2
# docker rmi fedora/2017-03-01
Untagged: fedora/2017-03-01:latest
Deleted: sha256:1f8ec1108a3fd027acd5d0bcc574afc179d15e9469b887e015d92116b853eed8
Deleted: sha256:bf973ec11f37bce787f28f7ccf15aa42e6c20188c5267caea4973f802edec873
```
* Import/export docker images
```bash
# docker load -i ${DOCKER_IMAGE_FILE}
# docker save ${DOCKER_IMAGE_NAME} > ${DOCKER_IMAGE_FILE}
```
