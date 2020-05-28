*1. create new repository 
* sudo svnadmin create /home/svn/repo
*2.update from 1.4.2 to 1.8
* svnrdump dump http://192.168.0.215/svn/allcommail >allcommail.dmp
* svnrdump dump http://192.168.0.215/svn/allcomemail >allcomemail.dmp
* sudo svnadmin load /home/svn/repo  <allcommail.dmp 
* sudo svnadmin load /home/svn/repo  <allcomemail.dmp 
* sudo killall svnserve
* sudo svnserve -d -r /home/svn

https://www.linuxtechi.com/install-apache-subversion-svn-centos-7/
sudo htpasswd -cm /etc/svn-auth-accounts liwei


http://svnbook.red-bean.com/zh/1.8/svn.reposadmin.maint.html
