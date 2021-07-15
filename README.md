# Proxy Squid


```bash
icap_enable on
icap_service service_req reqmod_precache bypass=1 icap://192.168.183.14:1344/request
adaptation_access service_req allow all
icap_send_client_ip on
icap_send_client_username on

acl SSL_ports port 443
acl Safe_ports port 80          # http
acl Safe_ports port 21          # ftp
acl Safe_ports port 443         # https
acl Safe_ports port 70          # gopher
acl Safe_ports port 210         # wais
acl Safe_ports port 1025-65535  # unregistered ports
acl Safe_ports port 280         # http-mgmt
acl Safe_ports port 488         # gss-http
acl Safe_ports port 591         # filemaker
acl Safe_ports port 777         # multiling http
acl CONNECT method CONNECT
acl localnet src 192.168.183.0/24
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports
http_access allow localhost manager
http_access deny manager
http_access allow localhost
http_access allow localnet
http_access deny all



http_port 192.168.183.12:3128 intercept ssl-bump generate-host-certificates=on dynamic_cert_mem_cache_size=16MB  cert=/etc/squid/public.pem key=/etc/squid/private.pem
https_port 192.168.183.12:3129 intercept ssl-bump generate-host-certificates=on dynamic_cert_mem_cache_size=16MB  cert=/etc/squid/public.pem key=/etc/squid/private.pem

acl step1 at_step SslBump1
acl step2 at_step SslBump2
acl step3 at_step SslBump3

ssl_bump peek step1 all
ssl_bump bump step2 all
ssl_bump bump step3 all

sslcrtd_program /usr/lib/squid/ssl_crtd -s /var/lib/ssl_db -M 16MB


coredump_dir /var/spool/squid
refresh_pattern ^ftp:           1440    20%     10080
refresh_pattern ^gopher:        1440    0%      1440
refresh_pattern -i (/cgi-bin/|\?) 0     0%      0
refresh_pattern (Release|Packages(.gz)*)$      0       20%     2880
refresh_pattern .               0       20%     4320
```
