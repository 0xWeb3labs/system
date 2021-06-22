ssh root@192.168.202 or 185

root/fa pi/pi

http://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO_Plus2/zh
https://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO_Plus2#Setup_Wi-Fi_Hotspot

## set up wifi router
https://askubuntu.com/questions/180733/how-to-create-a-wi-fi-hotspot-in-access-point-mode
```
#turn-wifi-into-apmode yes
For Internet connection sharing we need ip forwarding and ip masquerading. Enable ip forwarding : execute

#echo 1| sudo tee /proc/sys/net/ipv4/ip_forward
#iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j MASQUERADE
```
### set up wifi for ubuntu
https://www.cyberciti.biz/faq/debian-ubuntu-linux-setting-wireless-access-point/

https://help.ubuntu.com/community/Internet/ConnectionSharing
#### check 
nstall iw & execute following

sudo aptitude install iw
iw list

#### 1. Setup & host a wireless network
hostapd
```
vi /etc/hostapd/hostapd.conf
interface=wlan0
driver=nl80211
ssid=test
hw_mode=g
channel=1
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=3
wpa_passphrase=1234567890
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
# service hostapd start
```
#### 2. IP address setup
install isc-dhcp-server
```
/etc/default/isc-dhcp-server
INTERFACES="wlan0"
subnet 10.10.0.0 netmask 255.255.255.0 {
        range 10.10.0.2 10.10.0.16;
        option domain-name-servers 8.8.4.4, 208.67.222.222;
        option routers 10.10.0.1;
}
#vi /etc/network/interfaces
auto wlan0
iface wlan0 inet static
address 10.10.0.1
netmask 255.255.255.0
#service isc-dhcp-server start
```
#### 3. Internet sharing
```
For Internet connection sharing we need ip forwarding and ip masquerading. Enable ip forwarding : execute

#echo 1| sudo tee /proc/sys/net/ipv4/ip_forward
#sudo iptables -t nat -A POSTROUTING -s 10.10.0.0/16 -o ppp0 -j MASQUERADE
```
#### 4. Transparent Proxy
https://guide.v2fly.org/en_US/app/transparent_proxy.html#procedures

https://www.v2ray.com/chapter_02/protocols/dokodemo.html

https://toutyrater.github.io/app/tproxy.html
