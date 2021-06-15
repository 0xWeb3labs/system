## Centos 6 repo error
https://www.getpagespeed.com/server-setup/how-to-fix-yum-after-centos-6-went-eol
```
curl https://www.getpagespeed.com/files/centos6-eol.repo --output /etc/yum.repos.d/CentOS-Base.repo
curl https://www.getpagespeed.com/files/centos6-epel-eol.repo --output /etc/yum.repos.d/epel.repo
```
## Centos 6 Update to 7
https://www.cnblogs.com/larrypeng/p/11539597.html

```
yum install redhat-upgrade-tool preupgrade-assistant-contents -y
preupg -l
preupg -s CentOS6_7
wget https://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso
centos-upgrade-tool-cli --iso=/tmp/CentOS-7-x86_64-DVD-2009.iso
```
http://blog.sina.com.cn/s/blog_13ea37b210102x9ah.html
