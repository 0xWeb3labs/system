# system
OS related

#open port

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

