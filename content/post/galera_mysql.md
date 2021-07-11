+++
title = "Galera MySQL"
date = 2017-07-11T16:29:26+08:00
lastmod = 2021-07-11T22:18:38+08:00
tags = ["hugo", "org", "emacs", "mysql", "galera"]
categories = ["emacs", "linux", "org"]
draft = false
+++

全centos7环境

| 10.69.0.10 | cmp-vi650rc4 |
|------------|--------------|
| 10.69.0.11 | cmp-d8uc6edi |
| 10.69.0.5  | cmp-g7msocxo |

添加host到 `/etc/hosts`

创建 `/etc/yum.repos.d/galera.repo`

```sh
cat > /etc/yum.repos.d/galera.repo <<EOF
[galera]
name = Galera
baseurl = http://releases.galeracluster.com/galera-3/centos/7/$basearch/
gpgkey = http://releases.galeracluster.com/GPG-KEY-galeracluster.com
gpgcheck = 1

[mysql-wsrep]
name = MySQL-wsrep
baseurl =  http://releases.galeracluster.com/mysql-wsrep-5.7/centos/7/$basearch/
gpgkey = http://releases.galeracluster.com/GPG-KEY-galeracluster.com
gpgcheck = 1
EOF
```

安装 `galera` 和 `mysql-wsrep`

```sh
yum update -y && yum install -y galera-3 mysql-wsrep-server-5.7 rsync
```

selinux 开启mysql的其他端口

```sh
semanage permissive -a mysqld_t
```

如果semanage找不到，请安装

```sh
yum install policycoreutils-python
```

启动mysql

```sh
systemctl start mysqld
```

修改mysql root 密码:

```sh
password_match=`awk '/A temporary password is generated for/ {a=$0} END{ print a }' /var/log/mysqld.log | awk '{print $(NF)}'`
echo "$password_match"
mysql  -uroot -p$password_match --connect-expired-password -e "ALTER USER 'root'@'localhost' IDENTIFIED BY  'Root-mysql-pw@2017'; FLUSH PRIVILEGES; "
```

```sql
grant all privileges on *.* to sst@'%' identified by '123456';
flush privileges;
```

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' identified by 'Root-mysql-pw@2017';
flush privileges;
```

修改 `/etc/my.cnf`

```conf
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
binlog_format=ROW
bind-address=0.0.0.0
default_storage_engine=innodb
innodb_autoinc_lock_mode=2
innodb_flush_log_at_trx_commit=0
innodb_buffer_pool_size=122M
wsrep_provider=/usr/lib64/galera-3/libgalera_smm.so
wsrep_provider_options="gcache.size=300M; gcache.page_size=300M"
wsrep_sst_method=rsync
wsrep_cluster_address="gcomm://10.69.0.10,10.69.0.11"
wsrep_cluster_name="mysql_cluster"

wsrep_node_address='10.69.0.10'
wsrep_node_name='node1'

!includedir /etc/my.cnf.d/

[mysql_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

在其他node上修改

```conf
wsrep_node_address='10.69.0.11'
wsrep_node_name='node2'
```

在第一台机器上执行:

```sh
/usr/bin/mysqld_bootstrap
```

后面的机器普通启动就好了

```sh
systemctl start mysqld
```
