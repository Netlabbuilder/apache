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
