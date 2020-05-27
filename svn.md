*1. create new repository 
* sudo svnadmin create /home/svn/repo
*2.update from 1.4.2 to 1.8
* svnrdump dump http://192.168.0.215/svn/allcommail >allcommail.dmp
* svnrdump dump http://192.168.0.215/svn/allcomemail >allcomemail.dmp
* sudo svnadmin load /home/svn/repo  <allcommail.dmp 
* sudo svnadmin load /home/svn/repo  <allcomemail.dmp 
* sudo killall svnserve
* sudo svnserve -d -r /home/svn
