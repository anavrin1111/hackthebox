# Keeper

## Intro

Keeper is an easy rated Linux machine from Hack The Box

## Enumeration

### Nmap

All tcp ports.

```
22/tcp    open     ssh           syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp    open     http          syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
```

### Directory fuzzing

Fuzzing tickets sub domain.

```
m
rt
rt3
rta
rtb
rtc
rtds
rte
rteeditor
rte-snippets
rtest
rtf
rtg
rti
rtl
rtl2
rtm
rtq
rtr
rts
rttc
rtv
rtw
```

### Default credentials

The following default credentials were found researching Best Practical request tracker.  

```
username: root
password: password
```

## User

Looking through the admin panel, under users there is a helpdesk agent with a password written in the comment.  

```
lnorgaard@keeper.htb
Welcome2023!
```


## Root

In the home folder of lnorgaard, there is a zip file RT30000.zip that when unzipped gives a .dmp and .kdbx file.  
After trying to crack the hash with the following commands.  

```
keepass2john passcodes.kdbx > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

This was tried with danish wordlists as the admin portal gave information of the user being from Denmark.  

Upon researching and reading an email stating should the Windows user update keepass, I discovered a cve.  

```
https://github.com/vdohney/keepass-password-dumper.git
```

This gave the partial password, which after googling I discovered was:  

```
rødgrød med fløde
```

After downloading keepass2 for linux, it kept crashing due to the non ascii danish character, so after researching the bug I found an alternative.  

```
sudo apt install keepassxc
```

This showed a root password in the .kdbx file which did not work, but also a .ppk file which is a putty ssh private key.  

Unable to convert to OpenSSH with linux puttygen due to the key format being too new, I used putty on my windows system.  

```
puttygen -> load key -> conversions -> export OpenSSH
```

This private OpenSSH key enabled me to ssh as root.
