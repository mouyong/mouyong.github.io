---
title: 搭建 OpenVpn
date: 2019-07-29 16:45:45
categories: 工具
tags: 工具
english_title: Build-OpenVPN
---

## 安装

**注：当选择 DNS 时，请尽量选择 2 ~ 4 的值，原因见：https://github.com/Nyr/openvpn-install/issues/629#issuecomment-509107936**

```
# ubuntu 16.04
wget https://git.io/vpn1604 -O openvpn-install.sh && bash openvpn-install.sh

# ubuntu version >= 16.04
wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh
```

## 配置

server
```
port 1194
proto udp
dev tun
sndbuf 0
rcvbuf 0
ca ca.crt
cert server.crt
key server.key
dh dh.pem
auth SHA512
tls-auth ta.key 0
topology subnet
server 10.8.9.0 255.255.255.0
ifconfig-pool-persist ipp.txt
;push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 114.114.114.114"
keepalive 10 120
cipher AES-256-CBC
user nobody
group nogroup
persist-key
persist-tun
status openvpn-status.log
verb 5
crl-verify crl.pem
log openvpn-server.log
log-append openvpn-server.log
# 客户端互相访问
client-to-client
# 客户端单独配置路由的目录
client-config-dir ccd

# ipv6 相关
;server-ipv6 2001:0db8:ee00:abcd::/64
;tun-ipv6
;push tun-ipv6
;ifconfig-ipv6 2001:0db8:ee00:abcd::1 2001:0db8:ee00:abcd::2
;push "route-ipv6 2001:0db8:ee00:ee00::2/64"
;push "route-ipv6 2000::/3"
```

client

```
client
dev tun
proto udp
sndbuf 0
rcvbuf 0
remote youzan.cblink.net 1194
resolv-retry 5
nobind
persist-key
persist-tun
remote-cert-tls server
auth SHA512
cipher AES-256-CBC
;setenv opt block-outside-dns # 这里要注释掉，不然客户端不能访问互联网
key-direction 1
verb 3
auth-nocache # 这行是新增的
<ca>
</ca>
<cert>
</cert>
<key>
</key>
<tls-auth>
</tls-auth>
```

默认使用 udp 协议，1194 端口，根据安装过程中的选择，进行安全组设置。

- 腾讯云安全组帮助文档：https://cloud.tencent.com/document/product/213/34601