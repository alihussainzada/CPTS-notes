**FTP (File Transfer Protocol)** is a standard network protocol used for transferring files from one host to another over a TCP-based network, such as the Internet. It enables users to upload or download files, manage file directories on a remote server, and navigate the server's file system.

##### Note:
Press`ls -al` when you loged in.
Different port: `ftp -p 10.129.235.170 2121`
### Recon
- You can use nmap to identify
```BASH
nmap -p21 10.x.x.x
```
-  Banner Grabbing
```BASH
nc -nv 10.10.10.10 21
```
### Enumeration
- Using nmap?
```BASH
nmap -p 21 --script ftp-features <target-ip>
```
- Enumerating Default and Common Directories
```BASH
gobuster dir -u ftp://<target-ip> -w <wordlist-path>
```
### Attack Vectors
#### Anonymous Authentication
```BASH
ftp X.X.X.X

#provide anonymous as username
#provide any password
```
#### Common Credentials:
If anonymous login is disabled on the FTP server, trying common usernames and passwords like `admin`, `administrator` , `root` , `ftpuser`, or `test` can be a good initial step. This approach is less aggressive than attempting to guess passwords through brute force and is recommended to try first when accessing a server.

```
ftp X.X.X.X
#provide a common username
#provide a common password
```
#### Bruteforcing Credentials
- To use `Hydra` for brute-forcing FTP login credentials, you would use a command structured for this purpose:
```BASH
hydra [-L users.txt or -l user_name] [-P pass.txt or -p password] -f [-S port] ftp://X.X.X.X
```

##### Example:
```
hydra inlanefreight.htb -l fiona -P /usr/share/wordlists/rockyou.txt ftp -t 64
```
- It is also possible to perform brute force on FTP with `Nmap` scripts:
```BASH
nmap -p 21 --script ftp-brute X.X.X.X
```

 #### Download All Available Files
 ```BASH
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```

#### Upload a File
```BASH
touch testupload.txt

ftp> put testupload.txt 
```


## Post-Exploitation

### Common FTP Commands

| Command | Description                  | Usage                    |
| ------- | ---------------------------- | ------------------------ |
| `lcd`   | Change local directory.      | `lcd /path/to/directory` |
| `cd`    | Change server directory.     | `cd /path/to/directory`  |
| `ls`    | List server directory files. | `ls`                     |
| `get`   | Download file from server.   | `get filename.txt`       |
| `mget`  | Download multiple files.     | `mget *.txt`             |
| `put`   | Upload file to server.       | `put filename.txt`       |
| `mput`  | Upload multiple files.       | `mput *.txt`             |
| `bin`   | Set binary transfer mode.    | `bin`                    |
| `ascii` | Set ASCII transfer mode.     | `ascii`                  |
| `quit`  | Exit FTP client.             | `quit`                   |