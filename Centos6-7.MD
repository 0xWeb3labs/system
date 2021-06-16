## Centos 6 repo error
https://www.getpagespeed.com/server-setup/how-to-fix-yum-after-centos-6-went-eol
```
curl https://www.getpagespeed.com/files/centos6-eol.repo --output /etc/yum.repos.d/CentOS-Base.repo
curl https://www.getpagespeed.com/files/centos6-epel-eol.repo --output /etc/yum.repos.d/epel.repo
```
## Centos 6 Update to 7
https://www.cnblogs.com/larrypeng/p/11539597.html

### 1. get update tools
```
#proxychains4 yum install http://dev.centos.org/centos/6/upg/x86_64/Packages/openscap-1.0.8-1.0.1.el6.centos.x86_64.rpm
yum erase openscap -y
proxychains4 yum install https://buildlogs.centos.org/centos/6/upg/x86_64/Packages/openscap-1.0.8-1.0.1.el6.centos.x86_64.rpm
yum install redhat-upgrade-tool preupgrade-assistant-contents -y
```
### 2. pre upgrade check and failed fix
```
preupg -l
preupg -s CentOS6_7
```
#### 2.1 Remove Desktop 
Result for GNOME Desktop Environment underwent several design modifications in CentOS 7 release
Result: fail
yum groupremove Desktop

#### 2.2 /usr/ directory cannot be on a separate partition
```
Result for In-place Upgrade Requirements for the /usr/ Directory
Remediation instructions
Performing an in-place upgrade is not possible if the /usr/ directory is located on a separate partition. In CentOS 6, this was allowed, but in CentOS 7, the /usr/ directory cannot be on a separate partition because of the requirements of the systemd service.
              
DEBUG distribution: The script checks whether /usr directory is on separate partition.
DEBUG distribution: Checking /etc/fstab file
INPLACERISK: EXTREME: /usr directory is on separate partition. In-place Upgrade is NOT possible.

#vi /etc/fstab
UUID=8ad575f7-a8a5-415a-becd-ce328cbb979e /usr6                    ext4    defaults   
#mkdir /usr6
#tar zcvf usr.tar.gz /usr
#reboot in centos 6
#cd /
#tar zxvf usr.tar.gz
#reboot
```
### 3. get centos7 iso and do the upgrade
```
wget https://mirrors.aliyun.com/centos-vault/7.2.1511/isos/x86_64/CentOS-7-x86_64-DVD-1511.iso
centos-upgrade-tool-cli  -f -v --reboot --iso=/home/iso/CentOS-7-x86_64-DVD-1511.iso
wget https://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso
centos-upgrade-tool-cli --iso=/tmp/CentOS-7-x86_64-DVD-2009.iso
```
http://blog.sina.com.cn/s/blog_13ea37b210102x9ah.html

### 4. post upgrade
https://blog.csdn.net/qingkong999/article/details/79114925/
#### 4.1 get repo for centos 7
```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all
yum makecache
```
#### 4.2 fix missing libs and start sshd for remote access
```
ln -s /usr/lib64/libsasl2.so.3.0.0 /usr/lib64/libsasl2.so.2
ln -s /usr/lib64/libpcre.so.1.2.0 /usr/lib64/libpcre.so.0
yum install xz-compat-libs
service sshd restart
```
#### 4.3 remove old packages and update centos 7
```
yum remove kernel-debug-2.6.32-754.29.2.el6.x86_64
yum remove grubby-8.28-17.el7.x86_64
yum remove kernel-debug-2.6.32-754.31.1.el6.x86_64
yum remove kernel-debug-2.6.32-220.el6.x86_64
yum remove plymouth-theme-rings-0.8.3-29.el6.centos.noarch
yum remove python-argparse-1.2.1-2.1.el6.noarch
yum remove pyxf86config-0.3.37-7.1.el6.x86_64 -y
yum remove gnome-python2-libwnck-2.28.0-5.el6.x86_64 -y
yum update -y
```
