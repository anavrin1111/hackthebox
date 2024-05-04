# Legacy (easy/TJ Null list)  

## Nmap  

Port 0-15000.  

```
135/tcp open  msrpc        syn-ack Microsoft Windows RPC
139/tcp open  netbios-ssn  syn-ack Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds syn-ack Windows XP microsoft-ds
```

OS smb discovery.  

```
OS: Windows XP (Windows 2000 LAN Manager)
OS CPE: cpe:/o:microsoft:windows_xp::-
Computer name: legacy
NetBIOS computer name: LEGACY\x00
```  

UDP top ports 100.  

```
123/udp   open          ntp             udp-response ttl 127
137/udp   open          netbios-ns      udp-response ttl 127
```  

## SMB  

SMB version 1 is running, so the following is added to '/etc/samba/smb.conf' global section.  

`client min protocol = NT1`  

And the following option is added to smbclient command.  

`smbclient -L '\\10.10.1.230\' --option='client min protocol=NT1' -U '' -N`  

The above command attemtping to list shares failed with access denied.  

Using nmap enum scripts, the following was guessed and returned as anonymous read.  

`IPC$`  

This gives an access denied when connected and attempting to list contents of the share.  

## SMB vuln  

Running a nmap scan.  

`nmap -Pn -vvv --script=smb-vuln-* 10.10.1.230`  

The following vulnerabilities are returned for further investigation.  

```
 Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
```  

```
 VULNERABLE:
|   Microsoft Windows system vulnerable to remote code execution (MS08-067)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2008-4250
|           The Server service in Microsoft Windows 2000 SP4, XP SP2 and SP3, Server 2003 SP1 and SP2,
|           Vista Gold and SP1, Server 2008, and 7 Pre-Beta allows remote attackers to execute arbitrary
|           code via a crafted RPC request that triggers the overflow during path canonicalization.
```  

## Shell/Exploit  

Using the following msfconsole module.  

`exploit/windows/smb/ms08_067_netapi`  

A shell is created with a user 'NT authority / system'. From here, a user flag is collected in 'john' desktop and root flag in administrator desktop.
