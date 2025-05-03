1. Install v2ray
```bash
bash <(curl -Ls https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```

2. edit config file:
```bash
 vim /usr/local/etc/v2ray/config.json
```

3. new config in pooldaar box

4.  check and run svc:
```bash
systemctl status v2ray.service

systemctl restart v2ray.service

ss -tulpn | grep 10808

```

5. install proxychains
```bash
apt install proxychains
```

6. edit proxychains config:
```bash
vim /etc/proxychains.conf

last line:
socks5  127.0.0.1 10808
```

```bash
proxychains curl facebook.com
```
