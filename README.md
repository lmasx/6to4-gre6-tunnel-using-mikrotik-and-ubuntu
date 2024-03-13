# 6to4-gre6-tunnel-using-mikrotik-and-ubuntu
میخوام با استفاده از سرور میکروتیک ایرانم که روش میکروتیک نصب کردم و اکانتینگ دارم روش به یک سرور خارجی تونل شش به چهار بزنم(نخند حال ندارم با کی بورد ور برم)

حالا چرا؟


چون ساختن میکروتیک زمان میبره و همه جا به راحتیه اوبونتو نمیشه نصبش کرد ولی الان ابر آروان تو ایران راحت کرده کار رو برای همین ساختن سرور میکروتیک روش راحته پس من سمت ایران یک میکروتیک گرفتم و کانفیگ کردمم و برای سمت خارج اوبونتو میزنم که دستم باز باشه برای عوض کردن تونل در صورت فیلتر شدن.


## داستان چیه؟

نصف کار تو اوبونتو نصف کار تو میکروتیکه بسیار ساده است و فقط نیازه که دقت کافی رو بکنی هرچی هم لازمه کامل گفتم.خط به خط اجرا کنید امیدوارم به نتیجه برسید.

### اول یک ماشین اوبونتو تهیه کنید
1-در یک کشور خارجی یک اوبونتو میگیریم


## Run this in order


```bash
  sudo -i
```

رمزت رو بزن تا روت بشی،بعدش به روز رسانی اوبونتو رو داریم
```bash
 apt-get update -y && apt-get upgrade -y
```

بعد یک فایل با ادیتور نانو توی این مسیر درست کنید با این دستور
```bash
  nano /etc/rc.local
```


بعدش این رو کپی کنید 

کلمه های ipIran رو به آی پی سرور میکروتیک ایران تغییر بدین و کلمه ipKharej هم به آی پی همین ماشین اوبونتو که دارین این بش رو روش اجرا میکنید تغییر بدید.بعدش سیوش کنید بیاید بیرون.
```bash
  #!/bin/bash
ip tunnel add 6to4tun_KH mode sit remote ipIran local ipKharej
ip -6 addr add 2001:470:1f10:e1f::2/64 dev 6to4tun_KH
ip link set 6to4tun_KH mtu 1480
ip link set 6to4tun_KH up

ip -6 tunnel add GRE6Tun_KH mode ip6gre remote 2001:470:1f10:e1f::1 local 2001:470:1f10:e1f::2
ip addr add 172.16.1.2/30 dev GRE6Tun_KH
ip link set GRE6Tun_KH mtu 1436
ip link set GRE6Tun_KH up

iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD  -j ACCEPT
sudo sysctl -w net.ipv4.ip_forward=1
echo "net.ipv4.ip_forward=1" > /etc/sysctl.conf
sysctl -p
service iptables save
service iptables restart
service iptables stop
service iptables start
```


