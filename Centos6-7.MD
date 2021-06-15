## Centos 6 repo error
```
curl https://www.getpagespeed.com/files/centos6-eol.repo --output /etc/yum.repos.d/CentOS-Base.repo
curl https://www.getpagespeed.com/files/centos6-epel-eol.repo --output /etc/yum.repos.d/epel.repo
```
## Centos 6 Update to 7
```
yum install redhat-upgrade-tool preupgrade-assistant-contents -y
preupg -l
preupg -s CentOS6_7
wget https://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso
centos-upgrade-tool-cli --iso=/tmp/CentOS-7-x86_64-DVD-2009.iso
```
https://www.cnblogs.com/larrypeng/p/11539597.html
