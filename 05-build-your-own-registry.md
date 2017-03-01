# Build Your Own Registry
```bash
# docker pull docker.io/registry
# docker run -d -p 5000:5000 docker.io/registry
# docker tag fedora/httpd localhost:5000/fedora/httpd
# docker push localhost:5000/fedora/httpd
# docker images localhost:5000/fedora/httpd
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
localhost:5000/fedora/httpd   latest              2e24ba2e7eea        About an hour ago   403.5 MB
```
