# 创建用户
```mysql
create user junhao identified by '123456';
```

## 授予权限
```mysql
#授予通过网络方式登录的junhao,对所有库所有表的权限
grant all privileges on *.* to junhao@'%' identified by '123456';

#给junhao用户使用本地命令行方式，授予某个库下的所有表的增删改查的权限
grant select,insert,delete,update on [TABLENAME].* to junhao@localhost identified by '123456';
```

# 设置隔离级别
每个数据库连接都有一个全局变量@@tx_isolation,表示当前的事务隔离级别

## 查看隔离级别
	select @@tx_isolation;
## 设置隔离级别
	set transaction isolation level read commit;
## 设置全局的隔离级别
	set global transaction isolation level read commit;
	
	
	




