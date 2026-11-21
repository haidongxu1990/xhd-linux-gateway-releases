# XHD Linux Gateway 网关面板

一款运行在 Linux VPS 上的多协议网关管理面板，提供 Web 管理界面，支持 L2TP/IPsec、Hysteria2、VLESS 等入站的创建与管理，以及用户、流量配额、到期时间和订阅管理。

## ⚠️ 授权说明（必读）

本软件为商业软件，**部署和使用都需要在线授权**：

- 程序运行时会连接授权服务器进行在线校验，未授权或授权过期将无法正常使用
- **授权费：99 元/年**（按服务器台数计）
- 本仓库的部署包可自由下载，但**下载不等于获得使用授权**
- 购买授权请通过 GitHub 联系作者开通

## 系统要求

- Ubuntu 22.04（其他 Debian 系发行版请自测），root 权限
- 云厂商安全组需放行：TCP 18088（管理面板）、UDP 500 / 4500 / 1701（L2TP/IPsec）

## 部署方法

1. 安装依赖：

```bash
apt update
apt install -y python3 python3-pip iptables nftables dnsmasq curl wget
pip3 install flask werkzeug requests jinja2 itsdangerous click blinker markupsafe
```

2. 从本仓库的 Releases 页面下载部署包 `xhd-linux-gateway-v98.tar.gz`，上传到 VPS 的 `/root` 目录。

3. 解压并安装：

```bash
cd /
tar -xzf /root/xhd-linux-gateway-v98.tar.gz
cd /opt/xhd-linux-gateway
chmod +x deploy.sh scripts/*.sh scripts/*.py
bash deploy.sh
```

4. 修改网关地址：编辑 `/opt/xhd-linux-gateway/gateway.env`，把 `GATEWAY_IP` 改成新 VPS 的公网 IP、`UPSTREAM_GATEWAY` 改成新 VPS 的网关 IP，然后重启面板：

```bash
systemctl restart xhd-gateway-web
```

5. 访问面板：`http://<VPS公网IP>:18088`，首次登录使用部署包内文档提供的默认管理员账号，**登录后请立即修改密码**。

6. 检查服务状态：

```bash
ss -tlnp | grep 18088
systemctl status xhd-gateway-web
```

## 使用说明

- 在面板中创建入站（L2TP/IPsec、Hysteria2、VLESS 等），为每个入站添加用户，并设置流量配额和到期时间
- L2TP 入站的用户行可一键复制客户端填写信息（名称、服务器地址、服务端口、用户名、密码、预共享密钥 PSK），粘贴到路由器或系统自带 VPN 设置中即可连接
- 授权状态可在面板内查看；授权到期前请及时续费，避免服务中断

## 卸载

```bash
cd /opt/xhd-linux-gateway
bash uninstall.sh
```

## 当前版本

- v98（2026-11-21）：修复 L2TP 流量重复结算（一场会话被结两次）的问题；更换新部署的默认预共享密钥（已部署服务器不受影响）
- 部署包 SHA256：`792b627085800dd4e51f3df88034ca60a05cec94f6b5569a38b550f71fcf0ed7`
- v97（2026-11-21）：优化 L2TP 分享信息复制格式（以入站名称为标题、每字段一行）
