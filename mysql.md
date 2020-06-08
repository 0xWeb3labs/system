#### 1. 安装
* Sever
```
https://www.linode.com/docs/databases/mysql/how-to-install-mysql-on-centos-7/
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum update
sudo yum install mysql-server
sudo systemctl start mysqld
```
* Just client
```
https://www.cnblogs.com/buxizhizhoum/p/11725588.html
[root@k8s-master ~]# rpm -ivh https://repo.mysql.com//mysql57-community-release-el7-11.noarch.rpm
[root@test1 ~]#
# 通过yum搜索
[root@test1 ~]# yum search mysql-community
[root@test1 ~]#
# 安装x64位的 mysql客户端
[root@test1 ~]# yum install mysql-community-client.x86_64 -y
```

#### 2.权限
```
use mysql;
update user SET password=PASSWORD("allcomMAIL") WHERE user='root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'allcomMAIL' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
