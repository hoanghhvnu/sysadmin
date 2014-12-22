# Mysql
##1. Character set

__*For each database:*__
`ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;`

__*For each table:*__
`ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;`

__*For each column:*__
`ALTER TABLE table_name CHANGE column_name column_name VARCHAR(191) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;`

##2. Remote access
```
GRANT ALL on <db.tbl> to 'root'@'<ip>' identified by '<pass>';
```
**Note:** MariaDB cannot grant on `*.*`
