#### 1. 安装
* https://www.linode.com/docs/databases/mysql/how-to-install-mysql-on-centos-7/

```
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum update
sudo yum install mysql-server
sudo systemctl start mysqld
```

#### 2.权限
```
use mysql;
update user SET password=PASSWORD("allcomMAIL") WHERE user='root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'allcomMAIL' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
