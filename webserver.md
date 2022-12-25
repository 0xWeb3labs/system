1. nginx
2. https://kalacloud.com/blog/how-to-install-nginx-on-ubuntu-20-04/
3. acme
```
5. curl  https://get.acme.sh | sh
6. ./acme.sh --register-account -m kadloop@gmail.com
[Fri Apr  8 07:44:54 UTC 2022] No EAB credentials found for ZeroSSL, let's get one
[Fri Apr  8 07:44:55 UTC 2022] Registering account: https://acme.zerossl.com/v2/DV90
[Fri Apr  8 07:45:03 UTC 2022] Registered
[Fri Apr  8 07:45:03 UTC 2022] ACCOUNT_THUMBPRINT='MMvY51gUNpEquIZdLDEZUl2k2DkCQ-6WEwuHwqqYNPs'
./acme.sh --issue -w /var/www/garage -d garage.org
web server with root at /var/www/garage should be up when run the above command
[Fri Apr  8 08:00:30 UTC 2022] Pending, The CA is processing your order, please just wait. (1/30)
[Fri Apr  8 08:00:33 UTC 2022] Success
[Fri Apr  8 08:00:33 UTC 2022] Verify finished, start to sign.
[Fri Apr  8 08:00:33 UTC 2022] Lets finalize the order.
[Fri Apr  8 08:00:33 UTC 2022] Le_OrderFinalize='https://acme-v02.api.letsencrypt.org/acme/finalize/488054160/78342923430'
[Fri Apr  8 08:00:34 UTC 2022] Downloading cert.
[Fri Apr  8 08:00:34 UTC 2022] Le_LinkCert='https://acme-v02.api.letsencrypt.org/acme/cert/0307ca3b22274da0891c29946135e454bc4b'
[Fri Apr  8 08:00:35 UTC 2022] Cert success.

```
if failed like  Processing, The CA is processing your order, please just wait. (29/30)
```
./acme.sh --set-default-ca --server letsencrypt
```
renew mat not work
```
ubuntu@ip-10-0-0-232:~$ .acme.sh/acme.sh --renew -d dns3.xyz
[Wed Jul 13 13:42:47 CST 2022] Renew: 'dns3.xyz'
[Wed Jul 13 13:42:47 CST 2022] Renew to Le_API=https://acme.zerossl.com/v2/DV90
[Wed Jul 13 13:42:57 CST 2022] Using CA: https://acme.zerossl.com/v2/DV90
[Wed Jul 13 13:42:57 CST 2022] Single domain='dns3.xyz'
[Wed Jul 13 13:42:57 CST 2022] Getting domain auth token for each domain
[Wed Jul 13 13:43:17 CST 2022] Getting webroot for domain='dns3.xyz'
[Wed Jul 13 13:43:17 CST 2022] Verifying: dns3.xyz
[Wed Jul 13 13:43:23 CST 2022] Processing, The CA is processing your order, please just wait. (1/30)
[Wed Jul 13 13:43:33 CST 2022] Processing, The CA is processing your order, please just wait. (2/30)
```
this works
```
 .acme.sh//acme.sh --issue -w /var/www/dns3.xyz -d dns3.xyz
 
 [Wed Jul 13 13:47:41 CST 2022] Using CA: https://acme-v02.api.letsencrypt.org/directory
[Wed Jul 13 13:47:41 CST 2022] Single domain='dns3.xyz'
[Wed Jul 13 13:47:41 CST 2022] Getting domain auth token for each domain
[Wed Jul 13 13:47:43 CST 2022] Getting webroot for domain='dns3.xyz'
[Wed Jul 13 13:47:43 CST 2022] Verifying: dns3.xyz
[Wed Jul 13 13:47:44 CST 2022] Pending, The CA is processing your order, please just wait. (1/30)
[Wed Jul 13 13:47:47 CST 2022] Success
[Wed Jul 13 13:47:47 CST 2022] Verify finished, start to sign.
[Wed Jul 13 13:47:47 CST 2022] Lets finalize the order.
[Wed Jul 13 13:47:47 CST 2022] Le_OrderFinalize='https://acme-v02.api.letsencrypt.org/acme/finalize/600103416/106311596286'
[Wed Jul 13 13:47:48 CST 2022] Downloading cert.
[Wed Jul 13 13:47:48 CST 2022] Le_LinkCert='https://acme-v02.api.letsencrypt.org/acme/cert/03c2c6c68b8755d173cd079c20ff525d38ec'
[Wed Jul 13 13:47:49 CST 2022] Cert success.

```
12/25/2022 - need to open http before acme
```
root@vultr:~/.acme.sh# ufw allow http
root@vultr:~/.acme.sh# ufw allow https
Rule added
Rule added (v6)
root@vultr:~/.acme.sh# ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere                  
3002/udp                   ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
443/tcp                    ALLOW       Anywhere                  
22 (v6)                    ALLOW       Anywhere (v6)             
3002/udp (v6)              ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
443/tcp (v6)               ALLOW       Anywhere (v6) 
```
```
 .acme.sh/acme.sh --register-account -m kadloop@gmail.com
 .acme.sh/acme.sh --issue -w /var/www/dns3 -d sg.dns3.xyz
~/.acme.sh/acme.sh --issue -w /var/www/dns3.xyz -d sg.dns3.xyz 
~/.acme.sh/acme.sh --issue -w /var/www/dns3.xyz -d dns3.xyz -d www.dns3.xyz
[Sun 25 Dec 2022 03:41:52 PM UTC] Your cert is in: /root/.acme.sh/dns3.xyz/dns3.xyz.cer
[Sun 25 Dec 2022 03:41:52 PM UTC] Your cert key is in: /root/.acme.sh/dns3.xyz/dns3.xyz.key
[Sun 25 Dec 2022 03:41:52 PM UTC] The intermediate CA cert is in: /root/.acme.sh/dns3.xyz/ca.cer
[Sun 25 Dec 2022 03:41:52 PM UTC] And the full chain certs is there: /root/.acme.sh/dns3.xyz/fullchain.cer

```
