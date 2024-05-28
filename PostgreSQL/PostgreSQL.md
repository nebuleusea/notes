*版本：PostgreSQL15*

## 配置文件
```shell
# 监听地址listen_addresses,端口port
vim /etc/postgresql/13/main/postgresql.conf

# IP访问权限
vim /etc/postgresql/15/main/pg_hba.conf

# 重启postgresql服务
systemctl restart postgresql
```

## 快捷命令
1. 列出数据库：\l
2. 进入、切换数据库：\c dbname
3. 列出角色或用户(\du = \dg)：\du、\dg
4. 列出schema(+:显示更详细信息)：\dn[+]
5. 列出表(+:显示更详细信息)：\dt[+] schema.*
6. 列出视图(+:显示更详细信息)：\dv[+] schema.*
7. 列出索引(+:显示更详细信息)：\di[+] schema.*
8. 列出pattern结构(+:显示更详细信息)：\d[+] schema.*/pattern
9. 显示表的权限分配情况：\dp、\z
10. 显示执行SQL语句的时间：\timing on/off
11. 退出sql命令行：\q

## 创建数据库
```postgresql
CREATE DATABASE [dbname];
```

```shell
createdb -h [PGHOST] -p [PGPORT] -U [PGUSER] [DBNAME]
```

## 连接数据库, 默认的用户和数据库是postgres
```shell
psql -h [PGHOST] -p [PGPORT] -U [PGUSER] -d [DBNAME] 
```

## 修改用户密码

* 快捷命令修改：\password [PGUSER]
* SQL语句修改 ↓
```postgresql
ALTER ROLE username with PASSWORD 'password'
ALTER USER username with PASSWORD 'password'
```

## 检查用户密码状态
```postgresql
SELECT rolname,rolpassword FROM pg_authid
```

## 查看用户的pattern权限
```postgresql
SELECT *
FROM information_schema.table_privileges
WHERE grantee = 'user_name'
```

## 授予
```postgresql
GRANT USAGE ON SCHEMA my_schema TO my_user;
GRANT SELECT ON my_table TO my_user;
GRANT SELECT ON ALL TABLES IN SCHEMA my_schema TO my_user;
```

## 撤销权限
```postgresql
GRANT USAGE ON SCHEMA my_schema TO my_user;
GRANT SELECT ON my_table TO my_user;
REVOKE ALL PRIVILEGES ON SCHEMA [SchemaName] FROM my_user;
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA [SchemaName] FROM jack;
```

## 导出
```shell
pg_dump -h [PGHOST] -p [PGPORT] -U [PGUSER] -Fc [DBNAME] > [FilePath:xxx.dump] 
```

## 导入
```shell
pg_restore -h [PGHOST] -p [PGPORT] -U [PGUSER] -d [DBNAME] [FilePath:xxx.dump] 
```
## Alter Table

1. Rename
    * 修改表名
    ```postgresql
    alter table TableName rename to NewTableName; 
    ```
    * 修改字段名
    ```postgresql
    alter table TableName rename column ColumnName to NewColumnName;
    ```

2. Add
    * 新增字段
    ```postgresql
    alter table TableName add ColumnName ColumnDataType;
    ```

## JSONB_OBJECT_KEYS函数
SELECT DISTINCT JSONB_OBJECT_KEYS(charge_item_amount)
FROM table


## EXTRACT函数
* EXTRACT() 函数接受两个参数：时间单位和要从中提取的日期时间值。
* 时间单位可以是年份 (YEAR)、月份 (MONTH)、日期 (DAY)、小时 (HOUR)、分钟 (MINUTE)、秒钟 (SECOND) 等等。
* 返回提取出的指定时间单位的值。

```postgresql
SELECT EXTRACT(YEAR FROM '2023-09-07 10:15:30')
```
