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
