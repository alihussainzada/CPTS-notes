### DIG - NS Query
```BASH
dig ns inlanefreight.htb @10.129.14.128
```

### DIG - ANY Query
We can use the option `ANY` to view all available records. This will cause the server to show us all available entries that it is willing to disclose.
```BASH
dig any inlanefreight.htb @10.129.14.128
```

### DIG - Version Query
```BASH
dig CH TXT version.bind 10.129.120.85
```

### Zone Transfer
AXFR query is a DNS protocol request used to retrieve all records of a domain from a DNS server:
#### Using dig
dig is the standard tool for DNS zone transfers:
```BASH
# Without specifying a domain
dig axfr @<DNS_IP>

# With specific domain
dig axfr @<DNS_IP> <DOMAIN>
```

#### Using fierce
fierce automates zone transfers and can perform dictionary attacks:
```
fierce --domain <DOMAIN> --dns-servers <DNS_IP>
```

### Enumeration
#### Automation
```BASH
dnsenum --dnsserver <DNS_IP> --enum -p 0 -s 0 -o subdomains.txt -f <WORDLIST> <DOMAIN>
```

