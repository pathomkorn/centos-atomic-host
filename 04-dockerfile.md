# Dockerfile
* New file with name "Dockerfile"
```
# WEB SERVER EXAMPLE

FROM docker.io/fedora
MAINTAINER ${MAINTAINER_NAME}

# SET INTERNET PROXY SERVER
RUN echo "proxy=http://${PROXY_SERVER}:${PROXY_PORT}/" >> /etc/dnf/dnf.conf

# INSTALL HTTPD SERVER
RUN dnf install -y httpd

# COMPOSE INDEX FILE
RUN echo 'hello container' > /var/www/html/index.html
```
* Build image from container
```bash
# docker build -t fedora/httpd-file .
Sending build context to Docker daemon 6.656 kB
Step 1 : FROM docker.io/fedora
Step 2 : MAINTAINER Pathomkorn Muttarak
Step 3 : RUN echo "proxy=http://${PROXY_SERVER}:${PROXY_PORT}/" >> /etc/dnf/dnf.conf
Step 4 : RUN dnf install -y httpd
Step 5 : RUN echo 'hello container' > /var/www/html/index.html
Successfully built 2c5d1b5e1962
```
