1. Install Apache:
```
[hungtx@linux ~]$ sudo dnf install httpd -y
```
2. Verify if Apache is installed:
```
[hungtx@linux ~]$ httpd -v
Server version: Apache/2.4.62 (AlmaLinux)
Server built:   Jan 10 2025 00:00:00
```
3. Verify if Apache is running:
```
hungtx@linux ~]$ systemctl status httpd
○ httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: inactive (dead)
       Docs: man:httpd.service(8)
```
4. Start Apache:
```
[hungtx@linux ~]$ systemctl start httpd
[hungtx@linux ~]$ systemctl status httpd
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: active (running) since Wed 2025-03-19 20:53:15 CET; 5s ago
       Docs: man:httpd.service(8)
   Main PID: 8923 (httpd)
     Status: "Started, listening on: port 80"
      Tasks: 177 (limit: 73454)
     Memory: 31.8M
        CPU: 97ms
     CGroup: /system.slice/httpd.service
             ├─8923 /usr/sbin/httpd -DFOREGROUND
             ├─8931 /usr/sbin/httpd -DFOREGROUND
             ├─8932 /usr/sbin/httpd -DFOREGROUND
             ├─8996 /usr/sbin/httpd -DFOREGROUND
             └─9008 /usr/sbin/httpd -DFOREGROUND

Mar 19 20:53:15 linux.fritz.box systemd[1]: Starting The Apache HTTP Server...
Mar 19 20:53:15 linux.fritz.box httpd[8923]: Server configured, listening on: port 80
Mar 19 20:53:15 linux.fritz.box systemd[1]: Started The Apache HTTP Server.
[hungtx@linux ~]$
```
5. Test if Apache is properly running on the same machine/server by opening the default page with hostname of `localhost` or loopback IP address of `127.0.0.1`:
```
http://localhost
```
```
http://127.0.0.1
```
