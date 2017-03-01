# Run Container
* Docker command
```
-p bind port on Atomic host to container
-i interactive
-t pseudo-tty
-v bind volumes on Atomic host to container
```
* Run first container
```bash
# docker run -i -t fedora /bin/bash
# echo "proxy=http://${PROXY_SERVER}:${PROXY_PORT}/" >> /etc/dnf/dnf.conf
# dnf install -y httpd
# echo 'hello container' > /var/www/html/index.html
# echo '#!/bin/bash' > /usr/sbin/httpd_startup.sh
# echo 'rm -rf /run/httpd' >> /usr/sbin/httpd_startup.sh
# echo 'install -m 710 -o root -g apache -d /run/httpd' >> /usr/sbin/httpd_startup.sh
# echo 'install -m 700 -o apache -g apache -d /run/httpd/htcacheclean' >> /usr/sbin/httpd_startup.sh
# echo 'exec /usr/sbin/apachectl -D FOREGROUND' >> /usr/sbin/httpd_startup.sh
# chmod 700 /usr/sbin/httpd_startup.sh
# exit
```
* Check containers
```bash
# docker ps
# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
dec5855ffbce        fedora              "/bin/bash"         3 minutes ago       Exited (0) 30 seconds ago                       compassionate_mahavira
```
* Commit container image
```bash
# docker commit ${CONTAINER_ID} fedora/httpd
sha256:2e24ba2e7eea2c5a258bc055235d8b707e0115f730940e371ddfc5f1f7e3575b
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
fedora/httpd        latest              2e24ba2e7eea        9 seconds ago       403.5 MB
docker.io/fedora    latest              1f8ec1108a3f        12 days ago         230.3 MB
```
* Remove stopped containers
```bash
# docker rm $(docker ps -a -q)
```
* Launch httpd container from image
```bash
# docker run -d -p 8080:80 fedora/httpd /usr/sbin/httpd_startup.sh
b41c37dc511eb864024665271b1f7777003e87337774e81b9bc30f740e6c3238
```
* Test HTTP Server by open http://${ATOMIC_HOST}:8080/
