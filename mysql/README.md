# Mysql配置脚本

拉取版本 8.0.33

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
vim my.conf
```

参考的配置文件如下（具体配置可以前往官网查看https://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html）

```
[mysqld]
#Mysql服务的唯一编号 每个mysql服务Id需唯一
server-id=1

#服务端口号 默认3306
port=3306

#mysql安装根目录（default /usr）
#basedir=/usr/local/mysql

#mysql数据文件所在位置
datadir=/var/lib/mysql

#pid
pid-file=/var/run/mysqld/mysqld.pid

#设置socke文件所在目录
socket=/var/lib/mysql/mysql.sock

#设置临时目录
#tmpdir=/tmp

# 用户
user=mysql

# 允许访问的IP网段
bind-address=0.0.0.0

# 跳过密码登录
#skip-grant-tables

#主要用于MyISAM存储引擎,如果多台服务器连接一个数据库则建议注释下面内容
#skip-external-locking

#只能用IP地址检查客户端的登录，不用主机名
#skip_name_resolve=1

#事务隔离级别，默认为可重复读，mysql默认可重复读级别（此级别下可能参数很多间隙锁，影响性能）
#transaction_isolation=READ-COMMITTED

#数据库默认字符集,主流字符集支持一些特殊表情符号（特殊表情符占用4个字节）
character-set-server=utf8mb4

#数据库字符集对应一些排序等规则，注意要和character-set-server对应
collation-server=utf8mb4_general_ci

#设置client连接mysql时的字符集,防止乱码
#init_connect='SET NAMES utf8mb4'

#是否对sql语句大小写敏感，1表示不敏感
lower_case_table_names=1

#最大连接数
max_connections=400

#最大错误连接数
max_connect_errors=1000

#TIMESTAMP如果没有显示声明NOT NULL，允许NULL值
explicit_defaults_for_timestamp=true

#SQL数据包发送的大小，如果有BLOB对象建议修改成1G
max_allowed_packet=128M

#MySQL连接闲置超过一定时间后(单位：秒)将会被强行关闭
#MySQL默认的wait_timeout  值为8个小时, interactive_timeout参数需要同时配置才能生效
interactive_timeout=1800
wait_timeout=1800

#内部内存临时表的最大值 ，设置成128M。
#比如大数据量的group by ,order by时可能用到临时表，
#超过了这个值将写入磁盘，系统IO压力增大
tmp_table_size=134217728
max_heap_table_size=134217728

#禁用mysql的缓存查询结果集功能
#后期根据业务情况测试决定是否开启
#大部分情况下关闭下面两项
#query_cache_size = 0
#query_cache_type = 0
 
#数据库错误日志文件
#log-error=/var/log/mysqld.log

#慢查询sql日志设置
#slow_query_log=1
#slow_query_log_file=/var/log/mysqld_slow.log

#检查未使用到索引的sql
log_queries_not_using_indexes=1

#针对log_queries_not_using_indexes开启后，记录慢sql的频次、每分钟记录的条数
log_throttle_queries_not_using_indexes=5

#作为从库时生效,从库复制中如何有慢sql也将被记录
log_slow_slave_statements=1

#慢查询执行的秒数，必须达到此值可被记录
long_query_time=8

#检索的行数必须达到此值才可被记为慢查询
min_examined_row_limit=100

#mysql binlog日志文件保存的过期时间，过期后自动删除
#expire_logs_days=5
binlog_expire_logs_seconds=604800
```

#### 4、赋权，防止挂载异常

```
chmod 777 /usr/local/docker/mysql/data
```

#### 5、运行镜像

```
docker run -itd -p 3306:3306 --restart=always --name mysql -v /usr/local/docker/mysql/conf/my.conf:/etc/mysql/my.cnf  -v /usr/local/docker/mysql/data:/var/lib/mysql -v /usr/local/docker/mysql/files:/var/lib/mysql-files/ -v /etc/localtime:/etc/localtime -v /usr/local/docker/mysql/log:/logs -e MYSQL_ROOT_PASSWORD=root mysql
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
ALTER user 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'awe2023zh!';
FLUSH PRIVILEGES;
exit
exit
```

**注：如果三方工具连不上记得看下防火墙是否开启**