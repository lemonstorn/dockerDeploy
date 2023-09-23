# Mysql配置脚本

拉取版本 8.0.33

# 执行以下 查找 配置文件的 命令：
```
mysql --verbose --help|grep -A 1 'Default options'

Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf /usr/local/mysql/etc/my.cnf ~/.my.cnf 
```

#### 1、拉取镜像

```
docker pull mysql
```

#### 2、创建mysql挂载路径

```
mkdir -p /usr/local/docker/mysql/conf
mkdir -p /usr/local/docker/mysql/log
mkdir -p /usr/local/docker/mysql/data
mkdir -p /usr/local/docker/mysql/files
```

#### 3、设置mysql 的配置文件

```
cd /usr/local/docker/mysql/conf
vim my.cnf
```

参考的配置文件如下（具体配置可以前往官网查看https://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html）

```
[mysqld]
#Mysql服务的唯一编号 每个mysql服务Id需唯一
server-id=1
#服务端口号 默认3306
port=3306
# 表示MySQL的管理用户
user = mysql

#指定服务器 默认 字符集,主流字符集支持一些特殊表情符号（特殊表情符占用4个字节）
character_set_server=utf8mb4 
#默认排序规则，注意要和character-set-server对应
collation-server = utf8mb4_unicode_ci
# 指定服务器 默认 引擎
default-storage-engine=INNODB

#mysql数据文件所在位置
datadir=/var/lib/mysql
#设置临时目录
#tmpdir=/tmp

# 允许访问的IP网段
bind-address=0.0.0.0

#只能用IP地址检查客户端的登录，不用主机名,禁用DNS主机名查找，启用以后请求响应快了一半
skip_name_resolve=1

#事务隔离级别，默认为可重复读，mysql默认可重复读级别（此级别下可能参数很多间隙锁，影响性能）
#transaction_isolation=READ-COMMITTED

#是否对sql语句大小写敏感，1表示不敏感
lower_case_table_names=1

#最大连接数
max_connections=100

#最大错误连接数
max_connect_errors=20

[mysqld_safe]
# 错误日志位置
log-error = /var/log/mysqld.log 

# 进程id文件
pid-file = /var/run/mysqld/mysqld.pid 


[mysql]
# mysql以socket方式运行的sock文件位置
socket=/data/var/mysql/mysql.sock
sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'


```

#### 4、赋权，防止挂载异常

```
chmod 777 /usr/local/docker/mysql/data
```

#### 5、运行镜像

```
docker run -itd -p 3306:3306 --restart=always --name mysql -v /usr/local/docker/mysql/conf/my.cnf:/etc/my.cnf  -v /usr/local/docker/mysql/data:/var/lib/mysql -v /usr/local/docker/mysql/files:/var/lib/mysql-files/ -v /etc/localtime:/etc/localtime -v /usr/local/docker/mysql/log:/logs -e MYSQL_ROOT_PASSWORD=root  -e TZ=Asia/Shanghai mysql --lower-case-table-names=1
```

命令解释

-v /usr/local/docker/mysql/conf:/etc/mysql：挂载配置文件

 -v /usr/local/docker/mysql/data:/var/lib/mysql ：挂载数据

 -v /usr/local/docker/mysql/files:/var/lib/mysql-files/：强制需要

 -v /etc/localtime:/etc/localtime：将容器时间与服务器时间同步

-e MYSQL_ROOT_PASSWORD=root：登陆root密码

#### 6、更新权限配置

```
docker exec -it mysql bash
mysql -uroot -proot
```

为root用户更新权限

```
grant all PRIVILEGES on *.* to root@'%' WITH GRANT OPTION;
```

#### 7、更新密码

设置root用户密码永不过期，由于Mysql5.6以上的版本修改了Password算法，这里需要更新密码算法，便于使用三方工具连接

```
ALTER user 'root'@'%' IDENTIFIED BY 'awe2023zh!' PASSWORD EXPIRE NEVER;
FLUSH PRIVILEGES;
exit
exit
```

**注：如果三方工具连不上记得看下防火墙是否开启**

**注：mysql8默认使用sha256_password和caching_sha2_password两种密码认证插件，部分三方工具可能版本太老不支持**

清空表数据

select CONCAT('TRUNCATE TABLE ',table_name,';') from information_schema.tables where TABLE_SCHEMA = 'bladex';