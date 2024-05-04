# Perfection

## Info

Perfection is an easy rated linux machine from Hack The Box.

## Nmap

The following tcp ports are open.

```
22
80
```

## User

The grade calculator is vulnerable to SSTI. The regex filter can be bypassed by putting a \
string with a newline before and after the payload. Ruby matches up until a newline.

The following payload in the category name produces a 49.

```
test%0A%3C%25%3D%207%2A7%20%25%3E%0Atest
```

Using the ruby system() function, I transfered a python reveerse shell to tmp and execute.


## Root

There is an email in mail stating a new password format - first name, first name reverse and random int to a billion.
The pupilpath credentials .db file has susan sha256 password.
After created a wordlist, I cracked with hashcat module 1400.

susan creds.

```
susan_nasus_413759210
```

susan has all/all sudo permission.
