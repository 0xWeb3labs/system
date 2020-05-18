将/etc/yum.repos.d/CentOS-Base.repo中的片段改为这样（有好几处）：
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&amp;arch=$basearch&amp;repo=os
baseurl=http://vault.centos.org/5.11/os/$basearch/
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
yum makecache
yum update

https://www.geekzu.cn/archives/centos-5x-yum-error.html
