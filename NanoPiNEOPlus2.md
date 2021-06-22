ssh root@192.168.202 or 185

root/fa pi/pi

http://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO_Plus2/zh


## set up wifi router
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j MASQUERADE
```
