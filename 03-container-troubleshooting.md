# Container Troubleshooting
* Enter container namespace
```bash
# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
b41c37dc511e        fedora/httpd        "/root/httpd.sh"    2 minutes ago       Up 2 minutes        0.0.0.0:8080->80/tcp   elated_shannon
# docker top b41c37dc511e
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                14473               14457               0                   16:39               ?                   00:00:00            /bin/sh /usr/sbin/apachectl -D FOREGROUND
root                14500               14473               0                   16:39               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14501               14500               0                   16:39               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14502               14500               0                   16:39               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14503               14500               0                   16:39               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14504               14500               0                   16:39               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14508               14500               0                   16:39               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14532               14500               0                   16:40               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14535               14500               0                   16:40               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
48                  14536               14500               0                   16:40               ?                   00:00:00            /usr/sbin/httpd -D FOREGROUND
# nsenter -m -u -n -i -p -t 14473 /bin/bash
# tail -100f /var/log/httpd/access_log
# docker exec -i -t b41c37dc511e echo "hi"
```
