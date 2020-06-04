# system
OS related

#open port - firewall

firewall-cmd --zone=public --add-port=3000/tcp --permanent

firewall-cmd --reload

Use this command to find your active zone(s):

firewall-cmd --get-active-zones

It will say either public, dmz, or something else. You should only apply to the zones required.

In the case of public try:

firewall-cmd --zone=public --add-port=2888/tcp --permanent

Then remember to reload the firewall for changes to take effect.

firewall-cmd --reload

Otherwise, substitute public for your zone, for example, if your zone is dmz:

firewall-cmd --zone=dmz --add-port=2888/tcp --permanent

#IPTABLES
https://stackoverflow.com/questions/19034542/how-to-open-port-in-centos


First, you should disable selinux, edit file /etc/sysconfig/selinux so it looks like this:
```
SELINUX=disabled
SELINUXTYPE=targeted
```
Save file and restart system.

Then you can add the new rule to iptables:

iptables -A INPUT -m state --state NEW -p tcp --dport 8080 -j ACCEPT

and restart iptables with /etc/init.d/iptables restart

### sftp
sftp -oPort=2323 xxx@gxxxxx
rsync -avz -e 'ssh -p 2222' /data/filestore liwei@gfax.org:/home/liwei/data/GFAX/filestore 
