##### Tested on NanoPi NEO Plus2 and Untuntu PC box with WiFi
https://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO_Plus2#Setup_Wi-Fi_Hotspot

## 1. ap and router by iptables
```
#turn-wifi-into-apmode yes
#vi /etc/wpa_supplicant/wpa_supplicant.conf

For Internet connection sharing we need ip forwarding and ip masquerading. Enable ip forwarding : execute

** two line to implement basic router using iptables
** this is very important otherwise there is no voice fromclubhouse
#echo 1| sudo tee /proc/sys/net/ipv4/ip_forward
#iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j MASQUERADE
```
## 2. v2ray (option)
any other vpn service should be ok

https://www.v2ray.com/chapter_00/install.html

https://github.com/v2fly/fhs-install-v2ray
```
// 安裝執行檔和 .dat 資料檔
# bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)

  
```  
## 3. clash
### 3.1 clash basic config
```
port: 7890
socks-port: 7891
redir-port: 7892
allow-lan: true
mode: Rule
log-level: info
external-controller: 0.0.0.0:9090
secret: ""
external-ui: dashboard
#此处内容请安装一个gui版本的clash然后在里面配置好代理然后抄过来
Proxy: 
 # http
  - name: "http"
    type: http
#    server: 192.168.0.176
    server: 127.0.0.1
    port: 10809
Proxy Group:
#
Rule:
- IP-CIDR,127.0.0.0/8,DIRECT
- IP-CIDR,192.168.0.0/16,DIRECT
- FINAL,Proxy
```
### 3.2 clash ui
```
cd ~/.config/
mkdir clash
touch config.yaml
wget https://github.com/haishanh/yacd/archive/gh-pages.zip
unzip gh-pages.zip
mv yacd-gh-pages/ dashboard/
```
### 3.3 start clash automatically as servie
```
#/etc/systemd/system/clash.service
[Unit]
Description=clash service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/clash
Restart=on-failure # or always, on-abort, etc

[Install]
WantedBy=multi-user.target
```
## 4. iptables configuration work with clash
```
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
### save iptables
```
sudo apt install iptables-persistent netfilter-persistent
sudo netfilter-persistent save
sudo netfilter-persistent reload```
