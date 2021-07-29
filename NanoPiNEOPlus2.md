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
#### check and prepare
nstall iw & execute following

sudo aptitude install iw
iw list

better remove avahi-daemon

https://superuser.com/questions/316715/removing-the-avahi-daemon-on-ubuntu
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
use ss-redir (shadowsocks client) of shadowsocks and iptables to redirect the traffic to ss-redir
https://breakertt.moe/2019/08/20/clash_gateway/

##### 4.0 clash+iptables
should be the simplest way

http://blog.joylau.cn/2020/05/01/Clash-Config/

https://docs.cfw.lbyczf.com/contents/ui/profiles/rules.html

###### DNS related 
https://tachyondevel.medium.com/%E6%BC%AB%E8%B0%88%E5%90%84%E7%A7%8D%E9%BB%91%E7%A7%91%E6%8A%80%E5%BC%8F-dns-%E6%8A%80%E6%9C%AF%E5%9C%A8%E4%BB%A3%E7%90%86%E7%8E%AF%E5%A2%83%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8-62c50e58cbd0

fake-ip or redir-host
```
  default-nameserver:
    - 8.8.8.8
    - 192.168.0.119
    - 223.5.5.5
    - 114.114.114.114
  # enhanced-mode: fake-ip # 或 redir-host
  enhanced-mode: redir-host
  fake-ip-range: 198.18.0.1/16 # Fake IP 地址池 (CIDR 形式)
  # use-hosts: true # 查询 hosts 并返回 IP 记录

  # 在以下列表的域名将不会被解析为 fake ip，这些域名相关的解析请求将会返回它们真实的 IP 地址
  fake-ip-filter:
```
###### modify clash.yaml for clubhouse
```
  # 抗 DNS 污染
  - DOMAIN-SUFFIX,clubhouseapi.com,Proxy
  - DOMAIN-SUFFIX,clubhouseprod.s3.amazonaws.com,Proxy
  - DOMAIN-SUFFIX,clubhouse.pubnub.com,Proxy
  - DOMAIN-SUFFIX,maintenance.joinclubhouse.com,Proxy
  - DOMAIN-SUFFIX,ap3.agora.io,Proxy
```
###### Start and Web
```
/mnt/sdcard/clash-linux-armv7-v1.6.0 -f /mnt/sdcard/clash.yaml 
# clash 的 RESTful API 监听地址
external-controller: 0.0.0.0:9090

# 存放配置文件的相对路径，或存放网页静态资源的绝对路径
# Clash core 将会将其部署在 http://{{external-controller}}/ui
external-ui: /root/.config/clash/dashboard

iptables -t nat -N clash
iptables -t nat -A clash -d 0.0.0.0/8 -j RETURN
iptables -t nat -A clash -d 10.0.0.0/8 -j RETURN
iptables -t nat -A clash -d 127.0.0.0/8 -j RETURN
iptables -t nat -A clash -d 169.254.0.0/16 -j RETURN
iptables -t nat -A clash -d 172.16.0.0/12 -j RETURN
iptables -t nat -A clash -d 192.168.0.0/16 -j RETURN
iptables -t nat -A clash -d 224.0.0.0/4 -j RETURN
iptables -t nat -A clash -d 240.0.0.0/4 -j RETURN
iptables -t nat -A clash -d <local host ip> -j RETURN
iptables -t nat -A clash -p tcp -j REDIRECT --to-port 7892
iptables -t nat -I PREROUTING -p tcp -d 8.8.8.8 -j REDIRECT --to-port 7892
iptables -t nat -I PREROUTING -p tcp -d 8.8.4.4 -j REDIRECT --to-port 7892
iptables -t nat -A PREROUTING -p tcp -j clash

ip rule add fwmark 1 table 100
ip route add local default dev lo table 100
iptables -t mangle -N clash
iptables -t mangle -A clash -d 0.0.0.0/8 -j RETURN
iptables -t mangle -A clash -d 10.0.0.0/8 -j RETURN
iptables -t mangle -A clash -d 127.0.0.0/8 -j RETURN
iptables -t mangle -A clash -d 169.254.0.0/16 -j RETURN
iptables -t mangle -A clash -d 172.16.0.0/12 -j RETURN
iptables -t mangle -A clash -d 192.168.0.0/16 -j RETURN
iptables -t mangle -A clash -d 224.0.0.0/4 -j RETURN
iptables -t mangle -A clash -d 240.0.0.0/4 -j RETURN
iptables -t mangle -A clash -d <local host ip> -j RETURN
iptables -t mangle -A clash -p udp -j TPROXY --on-port 7892 --tproxy-mark 1
iptables -t mangle -A PREROUTING -p udp -j clash
```

https://guide.v2fly.org/en_US/app/transparent_proxy.html#procedures

https://www.v2ray.com/chapter_02/protocols/dokodemo.html

https://toutyrater.github.io/app/tproxy.html

https://blog.monsterxx03.com/2019/03/31/snet-transparent-ss-proxy-on-linux/

https://ivo-wang.github.io/2019/04/18/ss-redir+dnsmasq+iptables-%E9%80%8F%E6%98%8E%E4%BB%A3%E7%90%86/

##### 4.1 compile shadowsocks

https://github.com/shadowsocks/shadowsocks-libev/issues/2706
```
mkdir ~/source && ~/source
yum -y install gcc gettext autoconf libtool automake make pcre-devel asciidoc xmlto c-ares-devel libev-devel libsodium-devel mbedtls-devel
export LIBSODIUM_VER=1.0.18
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

# c-ares
$ sudo apt-get install libc-ares2 libc-ares-dev
or
apple@ubuntu:~/liwei$  git clone https://github.com/c-ares/c-ares.git


# compile and install
cd /tmp
git clone https://github.com/shadowsocks/shadowsocks-libev.git
cd shadowsocks-libev
git submodule update --init --recursive
./autogen.sh
./configure
make
make install
# cd ..
# rm -rf /tmp/shadowsocks-libev
#vi /etc/ss.conf
{  
"server":"192.168.0.94",  
"server_port":10801,  
"local_address":"0.0.0.0″,  
"local_port":10801,  
"password":"hello",  
"timeout":600,  
"method":"aes-256-cfb",  
"fast_open":false  
}

oot@NanoPi-NEO-Plus2:/usr/local/shadowsocks/bin# ./ss-redir -c /etc/shadowsocks.conf 
 2021-06-28 09:10:27 INFO: initializing ciphers... aes-256-cfb
 2021-06-28 09:10:27 INFO: Stream ciphers are insecure, therefore deprecated, and should be almost always avoided.
 2021-06-28 09:10:27 INFO: listening at 0.0.0.0:10801
 2021-06-28 09:10:27 INFO: running from root user
 2021-06-28 09:13:34 ERROR: server recv: Connection reset by peer
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
iptables -t nat -A SHADOWSOCKS -p tcp -j REDIRECT –to-ports 10801
#iptables -t nat -A SHADOWSOCKS -p tcp -j REDIRECT –to-destination 192.168.0.94:10801


# Apply the rules
#iptables -t nat -A OUTPUT -p tcp -j SHADOWSOCKS
# 对于本机全局代理必须是加入到OUTPUT链中，上面这句可以不加，至此脚本结束
```
iptables command
```
iptables -t nat -D  SHADOWSOCKS 29
iptables -t nat -L SHADOWSOCKS --line-number
####清除自定义规则
清空整个链 iptables -F 链名比如iptables -t nat -F redsock
删除指定的用户自定义链 iptables -X 链名 比如 iptables -t nat -F redsock
从所选链中删除规则 iptables -D 链名 规则详情 比如 iptables -t nat -D redsock -d 223.223.192.0/255.255.240.0 -j RETURN
```
