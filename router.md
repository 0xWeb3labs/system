## ap and router 
```
#turn-wifi-into-apmode yes
#vi /etc/wpa_supplicant/wpa_supplicant.conf

For Internet connection sharing we need ip forwarding and ip masquerading. Enable ip forwarding : execute

#echo 1| sudo tee /proc/sys/net/ipv4/ip_forward
#iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j MASQUERADE
```
## v2ray
https://github.com/v2fly/fhs-install-v2ray
```
  846  ln -s /root/tools/v2ray/v2ray /usr/local/bin/ 
  847  ln -s /root/tools/v2ray/v2ctl /usr/local/bin/ 
  849  mkdir /usr/local/share/v2ray
  850  mkdir /var/log/v2ray
  851  ln -s /root/tools/v2ray/geoip.dat /usr/local/share/v2ray/
  852  ln -s /root/tools/v2ray/geosite.dat /usr/local/share/v2ray/
  853  mkdir /usr/local/etc/v2ray
  854  ln -s /root/tools/v2ray/systemd/system/v2ray.service /etc/systemd/system/
  855  ln -s /root/tools/v2ray/systemd/system/v2ray@.service /etc/systemd/system/
  866  cp /mnt/sdcard/v2mac.conf /usr/local/etc/v2ray/config.json
  
```  
## clash
clash basic config
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
clash ui
```
cd ~/.config/
mkdir clash
touch config.yaml
wget https://github.com/haishanh/yacd/archive/gh-pages.zip
unzip gh-pages.zip
mv yacd-gh-pages/ dashboard/
```
clash as servie
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
##. iptables
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
