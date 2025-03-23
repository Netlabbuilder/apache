# Apache on AlmaLinux

Please follow these steps to install Apache HTTP Server on AlmaLinux machine or server

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
3. Verify if Apache is running, if the output shows `Active: inactive (dead)`, then the httpd.service is not running and is not started:
     ```
     hungtx@linux ~]$ systemctl status httpd
     ○ httpd.service - The Apache HTTP Server
          Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
          Active: inactive (dead)
            Docs: man:httpd.service(8)
     ```
4. Start Apache with `systemctl start httpd` command and verify its status again:
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
5. Check on which IP addresses the HTTP service is listening with `ss -lt` command.
   The output of `*:http` means the service is listening on all network interfaces (both for IPv4 and IPv6)
     ```
     hungtx@linux ~]$ ss -lt
     State   Recv-Q   Send-Q     Local Address:Port     Peer Address:Port  Process  
     LISTEN  0        4096           127.0.0.1:ipp           0.0.0.0:*              
     LISTEN  0        128              0.0.0.0:ssh           0.0.0.0:*              
     LISTEN  0        128                 [::]:ssh              [::]:*              
     LISTEN  0        511                    *:http                *:*              
     LISTEN  0        4096               [::1]:ipp              [::]:*              
     [hungtx@linux ~]$ 
     ```
6. Access the default AlmaLinux Test Page by openning `http://locahost` or `http://127.0.0.1` on the same AlmaLinux machine/server
7. To access the default AlmaLinux Test Page from other machines or clients, please first verify if firewalld service is running or not with `systemctl status firewalld.service`. If firewalld is running, verify if http service is allowed or not with `firewall-cmd --list-services`:
     ```
     [hungtx@linux ~]$ systemctl status firewalld.service 
     ● firewalld.service - firewalld - dynamic firewall daemon
          Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; preset: enabled)
          Active: active (running) since Sat 2025-03-22 09:54:48 CET; 6h ago
            Docs: man:firewalld(1)
        Main PID: 894 (firewalld)
           Tasks: 2 (limit: 73454)
          Memory: 47.6M
             CPU: 1.292s
          CGroup: /system.slice/firewalld.service
                  └─894 /usr/bin/python3 -s /usr/sbin/firewalld --nofork --nopid
     
     Mar 22 09:54:38 localhost systemd[1]: Starting firewalld - dynamic firewall daemon...
     Mar 22 09:54:48 localhost systemd[1]: Started firewalld - dynamic firewall daemon.
     [hungtx@linux ~]$ firewall-cmd --list-services 
     cockpit dhcpv6-client ssh
     [hungtx@linux ~]$
     ```
8. The output of `cockpit dhcpv6-client ssh` does not have http, this means http service is not allowed. Please allow http service with `firewall-cmd --add-service=http` command, then verify all allowed services again:
     ```
     [hungtx@linux ~]$ firewall-cmd --add-service=http
     success
     [hungtx@linux ~]$ firewall-cmd --list-services 
     cockpit dhcpv6-client http ssh
     ```
9. Access the default AlmaLinux Test Page from other machines / clients by openning `http://webserver_ip_address`, with `webserver_ip_address` is the non-local IP address of the AlmaLinux machine/server (on which Apache is installed)
10. Bonus - To work with Apache log files such as `/var/log/httpd/access_log` or other log files in the past `/var/log/httpd/access_log-YYYYMMDD`:
    - To extract unique IP addresses and count the number of requests per IP address, then sort (highest to lowest)
      ```
      [hungtx@linux ~]$ sudo awk '{print $1}' /var/log/httpd/access_log-20250323 | sort | uniq -c | sort -nr
          21 127.0.0.1
          14 192.168.178.27
           9 ::1
           6 192.168.178.59
           4 192.168.178.20
      [hungtx@linux ~]$
      ```
    - To extract unique IP addresses and count the number of requests with HTTP status code of `200` per IP address, then sort (highest to lowest):
      ```
      [hungtx@linux ~]$ sudo grep 200 /var/log/httpd/access_log-20250323 | awk '{print $1}' | sort | uniq -c | sort -nr
           7 127.0.0.1
           2 192.168.178.59
           2 192.168.178.27
           2 192.168.178.20
      [hungtx@linux ~]$ 
      ```

