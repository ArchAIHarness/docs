# OpenVPN Docker 部署指南

> **阅读对象**:平台工程师、运维工程师、需要临时 VPN 接入环境的开发者
> **适用场景**:使用 Docker 快速部署 OpenVPN,生成客户端 `.ovpn` 配置,并限制 VPN 客户端网络访问范围

本文基于 `kylemanna/openvpn` 镜像整理 OpenVPN Docker 部署流程,覆盖服务端初始化、客户端证书生成、防火墙配置、网络限制与常用管理命令。

## 1. 环境准备

确保 Docker 已安装并运行。

Ubuntu / Debian 示例:

```bash
sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker "$USER"
```

重新登录后验证:

```bash
docker --version
```

## 2. 初始化 OpenVPN 配置

### 2.1 创建数据卷

使用 Docker volume 持久化 OpenVPN 配置和证书:

```bash
docker volume create openvpn
```

后续命令统一挂载为 `openvpn:/etc/openvpn`。

### 2.2 生成服务器配置

将 `<vpn-domain>` 替换为真实域名或公网 IP。

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  kylemanna/openvpn \
  ovpn_genconfig -u tcp://<vpn-domain>:1194
```

示例:

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  kylemanna/openvpn \
  ovpn_genconfig -u tcp://vpn.example.com:1194
```

### 2.3 初始化 PKI

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  -it \
  kylemanna/openvpn \
  ovpn_initpki nopass
```

过程中如果提示输入 `Common Name`,可直接回车使用默认值,也可以填写 VPN 服务域名。

## 3. 启动 OpenVPN 服务

```bash
docker run -itd \
  -v openvpn:/etc/openvpn \
  -p 1194:1194/tcp \
  --name openvpn \
  --restart always \
  --sysctl net.ipv6.conf.default.forwarding=1 \
  --sysctl net.ipv6.conf.all.forwarding=1 \
  --cap-add NET_ADMIN \
  kylemanna/openvpn
```

验证服务:

```bash
docker ps -f name=openvpn
```

查看日志:

```bash
docker logs -f openvpn
```

## 4. 生成客户端配置

### 4.1 创建客户端证书

将 `<client-name>` 替换为客户端名称,例如 `phone`、`laptop`、`developer-01`。

无密码客户端证书:

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  -it \
  kylemanna/openvpn \
  easyrsa build-client-full <client-name> nopass
```

带密码客户端证书:

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  -it \
  kylemanna/openvpn \
  easyrsa build-client-full <client-name>
```

### 4.2 导出 `.ovpn` 文件

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  kylemanna/openvpn \
  ovpn_getclient <client-name> > <client-name>.ovpn
```

例如:

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  kylemanna/openvpn \
  ovpn_getclient developer-01 > developer-01.ovpn
```

将 `.ovpn` 文件导入 OpenVPN 客户端即可连接。

### 4.3 从服务器下载配置

如果 `.ovpn` 文件生成在远程服务器上,可用 `scp` 下载:

```bash
scp <user>@<server-ip>:/path/to/<client-name>.ovpn ./
```

## 5. 防火墙与网络配置

### 5.1 开放 OpenVPN 端口

以 UFW 为例:

```bash
sudo ufw allow 1194/tcp
```

### 5.2 启用 IPv4 转发

临时生效:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

永久生效:

```bash
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## 6. 网络访问限制

如果只希望 VPN 客户端访问公网,不允许访问内网网段,可以在宿主机或容器网络策略中增加转发规则。

### 6.1 默认策略与基础放行

```bash
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

iptables -A INPUT -i tun0 -j ACCEPT
iptables -A INPUT -p tcp --dport 1194 -j ACCEPT
```

### 6.2 禁止访问常见内网网段

```bash
iptables -A FORWARD -i tun0 -d 10.0.0.0/8 -j DROP
iptables -A FORWARD -i tun0 -d 172.16.0.0/12 -j DROP
iptables -A FORWARD -i tun0 -d 192.168.0.0/16 -j DROP
```

### 6.3 允许访问公网

在拒绝内网网段后,允许其他转发流量:

```bash
iptables -A FORWARD -i tun0 -j ACCEPT
iptables -A OUTPUT -o eth0 -j ACCEPT
```

`eth0` 需要按实际公网出口网卡调整。

### 6.4 保存规则

Ubuntu / Debian 可安装持久化工具:

```bash
sudo apt-get update
sudo apt-get install -y iptables-persistent
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

## 7. OpenVPN 配置示例

配置文件通常位于容器内:

```text
/etc/openvpn/openvpn.conf
```

示例配置片段:

```conf
server 192.168.255.0 255.255.255.0
verb 3
key /etc/openvpn/pki/private/<vpn-domain>.key
ca /etc/openvpn/pki/ca.crt
cert /etc/openvpn/pki/issued/<vpn-domain>.crt
dh /etc/openvpn/pki/dh.pem
tls-auth /etc/openvpn/pki/ta.key
key-direction 0
keepalive 10 60
persist-key
persist-tun

proto tcp
port 1194
dev tun0
status /tmp/openvpn-status.log

user nobody
group nogroup
comp-lzo no

push "dhcp-option DNS 10.96.0.10"
push "dhcp-option DOMAIN svc.cluster.local"
push "dhcp-option DOMAIN cluster.local"
push "dhcp-option DNS 114.114.114.114"
push "dhcp-option DNS 8.8.8.8"
push "comp-lzo no"
```

如不需要访问 Kubernetes 集群内 DNS,可移除 `10.96.0.10`、`svc.cluster.local`、`cluster.local` 相关配置。

## 8. 测试连接

测试端口连通性:

```bash
nc -zv <vpn-domain> 1194
```

成功示例:

```text
Connection to <vpn-domain> port 1194 [tcp/openvpn] succeeded!
```

客户端导入 `.ovpn` 后,连接成功即可验证路由、DNS 与访问范围。

## 9. 常用管理命令

### 9.1 查看日志

```bash
docker logs -f openvpn
```

### 9.2 停止和重启

```bash
docker stop openvpn
docker restart openvpn
```

### 9.3 吊销客户端证书

```bash
docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  -it \
  kylemanna/openvpn \
  easyrsa revoke <client-name>

docker run \
  -v openvpn:/etc/openvpn \
  --rm \
  -it \
  kylemanna/openvpn \
  easyrsa gen-crl

docker restart openvpn
```

### 9.4 删除服务但保留配置

```bash
docker rm -f openvpn
```

### 9.5 彻底删除服务和配置

```bash
docker rm -f openvpn
docker volume rm openvpn
```

## 10. 安全注意事项

- `.ovpn` 文件包含客户端证书和私钥,不要提交到代码仓库。
- 客户端证书应按人或设备单独生成,不要多人共用。
- 离职、设备丢失或权限回收时应吊销证书并重启服务。
- 生产环境应限制可访问网段,避免 VPN 客户端默认打通全部内网。
- 防火墙规则应先在测试环境验证,避免误断 SSH 或管理入口。
