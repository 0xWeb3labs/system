### 1. 权限
```
use mysql;
update user SET password=PASSWORD("allcomMAIL") WHERE user='root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'allcomMAIL' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
