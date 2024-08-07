# client
```
sudo wg show wg0
ip a show wg0
sudo systemctl enable wg-quick@wg0
sudo systemctl disable wg-quick@wg0
sudo wg-quick down wg0
[Interface]
PrivateKey = xxxxxx
Address = 10.0.2.2/24
DNS = 8.8.8.8, 8.8.4.4
MTU = 1384

[Peer]
PublicKey = 3VcMZdu0LeJxfw0asEEtfw7qPfYDDiidmtwaPGdtTE4=
AllowedIPs = 0.0.0.0/1, 128.0.0.0/1
Endpoint = xxxxx:51820
```
# server
```
sudo wg set wg0 peer CLIENT_PUBLIC_KEY allowed-ips 10.0.0.2
[Interface]
Address = 10.0.2.1/24
SaveConfig = true
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o ens5 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o ens5 -j MASQUERADE
ListenPort = 51820
PrivateKey = xxx5sci6TZEjUbLfVTAM6MekD/BQbRwsEUuSsjBQD18=
MTU = 1420

[Peer]
PublicKey = FLVmozGqjwvSdNcJEU7yxlK8CsfAgyujafomcZWtWXk=
AllowedIPs = 10.0.2.2/32
Endpoint = 183.193.153.81:6318

[Peer]
PublicKey = 4XSl0YpMNbOf14DT+v5af0+I9jm+x+63mrJdAauCDA8=
AllowedIPs = 10.0.2.3/32
Endpoint = 183.193.153.81:6286

[Peer]
PublicKey = Ed27fxwo8m+9UXg3gZ9pfEoZw4ldiIZSjcFXYBecgFs=
AllowedIPs = 10.0.2.4/32
Endpoint = 124.78.50.222:50431
```
# ubuntu
- https://linuxize.com/post/how-to-set-up-wireguard-vpn-on-ubuntu-20-04/
- https://www.linode.com/docs/guides/set-up-wireguard-vpn-on-ubuntu/
- https://zhuanlan.zhihu.com/p/138000980
- https://yooooex.com/2019/05/23/wireguard-deploy/
- https://seanzwrites.com/posts/hosting-from-home/

- https://icloudnative.io/posts/wireguard-docs-practice/

# centos 7
https://gist.github.com/tegila/ae203f1c5a4a7f99837136a541bbf502#set-up-your-own-wireguard-vpn-server-on-centos
https://www.hostafrica.com/blog/linux/install-wireguard-vpn-centos-7/)https://www.hostafrica.com/blog/linux/install-wireguard-vpn-centos-7/
