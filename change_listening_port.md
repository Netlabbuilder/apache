The default listening port on Apache HTTP Server is port 80 for HTTP traffic.

To change the default listening port from port 80 to another port, please modify the the `Listen` directive in the Apache configuration file `/etc/httpd/conf/httpd.conf`.

**IMPORTANT NOTE:** (Please see bullet point # 7) On SELinux-enforced systems, Apache HTTP Server can only listen on allowed ports. This means, if Apache HTTP Server is configured to listen on a port which is not allowed by SELinux, it will not work.

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
   The output line `Mar 23 14:54:29 linux.fritz.box httpd[8060]: Server configured, listening on: port 8008` indicates that the listening port has been changed to 8008, and the Apache HTTP Server is listening on port 8008.
5. Check the default AlmaLinux Test Page again:
    - With new URL of `http://locahost:8008` or `http://127.0.0.1:8008` on the AlmaLinux machine/server (on which the Apache is installed).
    - With new URL of `http://webserver_ip_address` from other machines/clients. The `webserver_ip_address` is the non-local IP address of the AlmaLinux machine/server (on which Apache is installed).
6. To configure Apache HTTP Server to listen on multiple ports, please use multiple `Listen` directives in the Apache configuration file `/etc/httpd/conf/httpd.conf`:
   ```
   [hungtx@linux ~]$ sudo cat /etc/httpd/conf/httpd.conf | grep Listen
   # Listen: Allows you to bind Apache to specific IP addresses and/or
   # Change this to Listen on a specific IP address, but note that if
   #Listen 12.34.56.78:80
   Listen 80
   Listen 8008
   [hungtx@linux ~]$ systemctl restart httpd
   [hungtx@linux ~]$ systemctl status httpd
   ● httpd.service - The Apache HTTP Server
        Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
        Active: active (running) since Sun 2025-03-23 16:34:52 CET; 4s ago
          Docs: man:httpd.service(8)
      Main PID: 11537 (httpd)
        Status: "Started, listening on: port 8008, port 80"
         Tasks: 177 (limit: 73454)
        Memory: 41.8M
           CPU: 90ms
        CGroup: /system.slice/httpd.service
                ├─11537 /usr/sbin/httpd -DFOREGROUND
                ├─11539 /usr/sbin/httpd -DFOREGROUND
                ├─11540 /usr/sbin/httpd -DFOREGROUND
                ├─11541 /usr/sbin/httpd -DFOREGROUND
                └─11542 /usr/sbin/httpd -DFOREGROUND
   
   Mar 23 16:34:52 linux.fritz.box systemd[1]: Starting The Apache HTTP Server...
   Mar 23 16:34:52 linux.fritz.box httpd[11537]: Server configured, listening on: port 8008, port 80
   Mar 23 16:34:52 linux.fritz.box systemd[1]: Started The Apache HTTP Server.
   [hungtx@linux ~]$
   ```
   The output of `Mar 23 16:34:52 linux.fritz.box httpd[11537]: Server configured, listening on: port 8008, port 80` indicates that Apache HTTP Server is listening on multiple ports 80 and 8008.
7. **IMPORTANT NOTE:** On SELinux-enforced systems, Apache HTTP Server can only listen on allowed ports. This means, if Apache HTTP Server is configured to listen on a port which is not allowed by SELinux, it will not work.
   - To verify is SELinux is enabled or not:
     ```
      [hungtx@linux ~]$ sestatus
      SELinux status:                 enabled
      SELinuxfs mount:                /sys/fs/selinux
      SELinux root directory:         /etc/selinux
      Loaded policy name:             targeted
      Current mode:                   enforcing
      Mode from config file:          enforcing
      Policy MLS status:              enabled
      Policy deny_unknown status:     allowed
      Memory protection checking:     actual (secure)
      Max kernel policy version:      33
     ```
   - To verify the current allowed ports for HTTP service by SELinux:
      ```
      hungtx@linux ~]$ sudo semanage port -l | grep "^http_port_t"
      http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
      [hungtx@linux ~]$
      ```
   - To allow a new TCP port which is not in allowed list (for example port 8080), please use the following command:
     ```
      sudo semanage port -a -t http_port_t -p tcp 8080
     ```
     `-a` --add, Add a record of the specified object type.\
     `-t` TYPE, --type TYPE, SELinux type for the object.\
     `-p` PROTO, --proto PROTO, Protocol for the specified port (tcp|udp|dccp|sctp) or internet protocol version for the specified node (ipv4|ipv6).\


