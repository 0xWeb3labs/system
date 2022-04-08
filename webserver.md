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
```
