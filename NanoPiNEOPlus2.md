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

https://blog.monsterxx03.com/2019/03/31/snet-transparent-ss-proxy-on-linux/

##### 4.1 compile shadowsocks

https://github.com/shadowsocks/shadowsocks-libev/issues/2706
```
mkdir ~/source && ~/source
yum -y install gcc gettext autoconf libtool automake make pcre-devel asciidoc xmlto c-ares-devel libev-devel libsodium-devel mbedtls-devel
export LIBSODIUM_VER=1.0.13
wget https://download.libsodium.org/libsodium/releases/libsodium-$LIBSODIUM_VER.tar.gz
tar xvf libsodium-$LIBSODIUM_VER.tar.gz
pushd libsodium-$LIBSODIUM_VER
./configure --prefix=/usr && make
make install
popd
ldconfig

export MBEDTLS_VER=2.6.0
wget https://tls.mbed.org/download/mbedtls-$MBEDTLS_VER-gpl.tgz
tar xvf mbedtls-$MBEDTLS_VER-gpl.tgz
pushd mbedtls-$MBEDTLS_VER
make SHARED=1 CFLAGS=-fPIC
make DESTDIR=/usr install
popd
ldconfig

# clean directory
rm -rf /tmp/shadowsocks-libev

# compile and install
cd /tmp
git clone https://github.com/shadowsocks/shadowsocks-libev.git
cd shadowsocks-libev
git submodule update --init --recursive
./autogen.sh
./configure
make
make install
cd ..
rm -rf /tmp/shadowsocks-libev
```
##### 4.2 iptables
```
#!/bin/bash
# Create new chain
iptables -t nat -N SHADOWSOCKS  
iptables -t nat -A PREROUTING -p tcp -j SHADOWSOCKS  
# Ignore your shadowsocks server’s addresses
# It’s very IMPORTANT, just be careful.
iptables -t nat -A SHADOWSOCKS -d 192.168.0.94 -j RETURN  
# 这一步非常重要！到服务端的出口数据不要再进行重定向，否则进入死循环
#去除内网地址
iptables -t nat -A SHADOWSOCKS -d 0.0.0.0 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 127.0.0.1 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 192.168.0.0/16 -j RETURN

iptables -t nat -A SHADOWSOCKS -d 8.8.0.0/16 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 0.0.0.0/8 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 10.0.0.0/8 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 127.0.0.0/8 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 169.254.0.0/16 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 172.16.0.0/12 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 192.168.0.0/16 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 224.0.0.0/4 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 240.0.0.0/4 -j RETURN  
iptables -t nat -A SHADOWSOCKS -d 0.0.0.0/254.0.0.0 -j RETURN
#还可以加如国内ip段参见 https://gist.github.com/wen-long/8644507
# Anything else should be redirected to shadowsocks’s local port
iptables -t nat -A SHADOWSOCKS -p tcp -j REDIRECT –to-ports 1080
#iptables -t nat -A SHADOWSOCKS -p tcp -j REDIRECT –to-destination 192.168.0.94:10801


# Apply the rules
#iptables -t nat -A OUTPUT -p tcp -j SHADOWSOCKS
# 对于本机全局代理必须是加入到OUTPUT链中，上面这句可以不加，至此脚本结束
```
