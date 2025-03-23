The default listening port on Apache HTTP Server is port 80 for HTTP traffic.

To change the default listening port from port 80 to another port, please modify the the `Listen` directive in the Apache configuration file `/etc/httpd/conf/httpd.conf`:

1. Verify the current listening port, the output shows `Listen 80`:
   ```
    [hungtx@linux ~]$ sudo cat /etc/httpd/conf/httpd.conf | grep Listen
    # Listen: Allows you to bind Apache to specific IP addresses and/or
    # Change this to Listen on a specific IP address, but note that if
    #Listen 12.34.56.78:80
    Listen 80
    [hungtx@linux ~]$
   ```
2. Use any text editor to change the port 80 to another port such as 8008:
   ```
    [hungtx@linux ~]$ sudo cat /etc/httpd/conf/httpd.conf | grep Listen
    # Listen: Allows you to bind Apache to specific IP addresses and/or
    # Change this to Listen on a specific IP address, but note that if
    #Listen 12.34.56.78:80
    Listen 8008
    [hungtx@linux ~]$
   ```
3. Run syntax tests for configuration files with `httpd -t` or `apachectl configtest`
   ```
    [hungtx@linux ~]$ httpd -t
    Syntax OK   
    [hungtx@linux ~]$ apachectl configtest
    Syntax OK
    [hungtx@linux ~]$
   ```
4. Restart httpd service and check its status again:
   ```
     [hungtx@linux ~]$ systemctl restart httpd
     [hungtx@linux ~]$ systemctl status httpd
      ● httpd.service - The Apache HTTP Server
           Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
           Active: active (running) since Sun 2025-03-23 14:54:29 CET; 39min ago
             Docs: man:httpd.service(8)
         Main PID: 8060 (httpd)
           Status: "Total requests: 6; Idle/Busy workers 100/0;Requests/sec: 0.00256; Bytes served/sec:   8 B/sec"
            Tasks: 177 (limit: 73454)
           Memory: 40.2M
              CPU: 1.547s
           CGroup: /system.slice/httpd.service
                   ├─8060 /usr/sbin/httpd -DFOREGROUND
                   ├─8068 /usr/sbin/httpd -DFOREGROUND
                   ├─8069 /usr/sbin/httpd -DFOREGROUND
                   ├─8070 /usr/sbin/httpd -DFOREGROUND
                   └─8071 /usr/sbin/httpd -DFOREGROUND
      
      Mar 23 14:54:29 linux.fritz.box systemd[1]: Starting The Apache HTTP Server...
      Mar 23 14:54:29 linux.fritz.box httpd[8060]: Server configured, listening on: port 8008
      Mar 23 14:54:29 linux.fritz.box systemd[1]: Started The Apache HTTP Server.
      [hungtx@linux ~]$
   ```
   The output line `Mar 23 14:54:29 linux.fritz.box httpd[8060]: Server configured, listening on: port 8008` indicates that the listening port has been changed to 8008
5. Check the default AlmaLinux Test Page again:
    - With new URL of `http://locahost:8008` or `http://127.0.0.1:8008` on the AlmaLinux machine/server (on which the Apache is installed).
    - With new URL of `http://webserver_ip_address` from other machines/clients. The `webserver_ip_address` is the non-local IP address of the AlmaLinux machine/server (on which Apache is installed)

