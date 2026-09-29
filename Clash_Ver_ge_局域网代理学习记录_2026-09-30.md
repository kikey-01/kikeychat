# Clash Verge 局域网代理学习记录

> 学习主题：使用 Windows 电脑上的 Clash Verge，为手机提供局域网代理上网  
> 学习时间：2026-09-30

---

## 一、今天最终实现的网络结构

今天最终成功实现了：

```text
手机
  │
  │ Wi-Fi / 局域网
  ▼
Windows 电脑
  │
  │ Clash Verge
  │ Mixed Port: 7897
  ▼
代理节点
  │
  ▼
Internet
```

也就是说，**手机不需要安装 Clash**，只需要把手机的 Wi-Fi 代理设置为电脑的局域网 IP + Clash 的代理端口即可。

---

# 二、Clash Verge 中几个重要概念

## 1. 系统代理

Windows 的系统代理开启后：

```text
Windows 应用
      ↓
系统代理
      ↓
Clash Verge
      ↓
代理节点 / DIRECT
```

它主要影响遵守 Windows 系统代理设置的软件。

但并不是所有程序都会遵守系统代理，因此：

> 开启系统代理 ≠ 电脑所有网络流量都会经过 Clash。

---

## 2. 局域网连接（Allow LAN）

开启「局域网连接」以后，Clash 可以接受来自局域网其他设备的连接。

例如：

```text
手机 192.168.1.110
        ↓
电脑 192.168.1.105:7897
        ↓
Clash
```

如果不开启局域网连接，通常只能让本机程序访问 Clash。

因此：

> 想让手机通过电脑上的 Clash，必须允许局域网设备访问 Clash 的代理端口。

---

## 3. Mixed Port（混合代理端口）

今天电脑上的配置：

```text
Mixed Port = 7897
```

Mixed Port 可以作为一个通用代理入口。

本次最终使用：

```text
电脑局域网IP:7897
```

作为手机的代理服务器。

例如电脑 IP 是：

```text
192.168.1.105
```

那么手机填写：

```text
服务器：192.168.1.105
端口：7897
```

---

## 4. SOCKS / HTTP 端口

当时的配置：

```text
SOCKS：7898
HTTP(S)：7899
Mixed：7897
```

本次没有使用 7898 和 7899，而是直接使用：

```text
7897
```

---

# 三、Clash 的 DIRECT、PROXY 是什么意思

Clash 使用规则模式时，不同连接可能有不同的处理方式。

例如：

```text
Google
   ↓
日本专线03
```

说明这个连接经过代理节点。

而：

```text
国内网站
   ↓
DIRECT
```

说明该连接直接连接目标服务器，而不是经过代理节点。

可以简单理解为：

| Clash 显示 | 含义 |
|---|---|
| DIRECT | 直接连接 |
| 日本专线03 | 经过这个代理节点 |
| PROXY | 经过代理 |
| REJECT | 拒绝连接 |

### 注意

看到一个网站使用 DIRECT，不代表整个网站的所有连接都一定是 DIRECT。

一个网站可能同时使用：

- 主站域名
- CDN
- API
- 图片服务器
- 第三方服务

因此判断实际走什么线路时，最好查看 Clash 的「连接」页面。

---

# 四、TUN / 虚拟网卡

今天也梳理了 TUN 的概念。

系统代理主要依赖应用是否遵守系统代理。

而 TUN / 虚拟网卡是在更底层接管网络流量，因此通常可以覆盖更多程序。

可以简单理解：

```text
系统代理：

应用
 ↓
系统代理
 ↓
Clash
```

而 TUN 更接近：

```text
应用
 ↓
操作系统网络层
 ↓
虚拟网卡 / TUN
 ↓
Clash
 ↓
Internet
```

### 重要认识

开启 TUN：

> 不会凭空产生更多“机场流量”。

但如果原来某些程序没有经过代理，开启 TUN 后它们被 Clash 接管，并且规则把它们送进代理节点，那么代理节点的流量自然会增加。

---

# 五、今天遇到的问题

目标是：

> 让手机通过电脑上的 Clash Verge 上网。

最开始已经确认 Clash 的：

```text
Mixed Port = 7897
```

并且已经开启：

```text
局域网连接
```

但是手机无法使用代理。

---

# 六、第一次重要排查：检查 7897 是否监听

在 Windows PowerShell / CMD 执行：

```powershell
netstat -ano | findstr :7897
```

当时得到：

```text
TCP    0.0.0.0:7897    0.0.0.0:0    LISTENING
```

以及：

```text
TCP    [::]:7897       [::]:0       LISTENING
```

这非常重要。

`0.0.0.0:7897` 表示：

> 7897 并不是只监听 127.0.0.1，而是在所有 IPv4 网络接口上监听。

因此可以排除：

> “Clash 只绑定 localhost，所以手机无法连接”

这个原因。

---

# 七、检查 Windows 防火墙

为了允许局域网设备访问 Clash 7897，创建了 Windows 防火墙规则。

管理员 PowerShell：

```powershell
New-NetFirewallRule `
-DisplayName "Clash Verge 7897 LAN" `
-Direction Inbound `
-Protocol TCP `
-LocalPort 7897 `
-Action Allow `
-RemoteAddress LocalSubnet
```

成功后看到：

```text
DisplayName : Clash Verge 7897 LAN
Enabled     : True
Profile     : Any
Direction   : Inbound
Action      : Allow
PrimaryStatus : OK
```

说明防火墙规则已经成功建立。

### 为什么使用 LocalSubnet？

因为我们的目的只是：

> 允许局域网里的手机访问 7897。

而不是：

> 让整个互联网都能访问电脑的 7897。

所以使用：

```text
-RemoteAddress LocalSubnet
```

比直接允许所有来源更加合理。

---

# 八、检查 7897 是否收到手机连接

执行：

```powershell
Get-NetTCPConnection -LocalPort 7897
```

最开始看到的主要是：

```text
127.0.0.1 → 127.0.0.1
```

例如：

```text
LocalAddress  LocalPort  RemoteAddress  RemotePort
127.0.0.1    7897       127.0.0.1      ...
```

这说明：

> 电脑自己的程序正在访问 Clash。

但没有看到：

```text
192.168.x.x → 电脑IP:7897
```

因此：

> 手机的连接没有真正到达电脑的 Clash 7897。

---

# 九、手机直接访问电脑 7897

为了进一步判断问题到底在哪里，让手机浏览器访问：

```text
http://电脑局域网IPv4:7897
```

例如：

```text
http://192.168.1.105:7897
```

当时第一次测试结果：

> 一直加载，最后超时。

后来进一步调整网络设置后，再次测试时：

> 手机立即提示无法连接或拒绝连接。

这进一步说明：

> 问题重点不在代理节点，而是在“手机 → 电脑”这一段。

所以没有继续折腾机场节点、规则和 TUN。

---

# 十、发现 Windows 网络类型是 Public

执行：

```powershell
Get-NetConnectionProfile
```

发现：

```text
NetworkCategory : Public
```

这意味着 Windows 把当前网络识别成：

> 公用网络

而我们希望家庭 / 私人局域网使用：

```text
Private
```

---

# 十一、把 Windows 网络改成 Private

首先查看：

```powershell
Get-NetConnectionProfile
```

找到：

```text
InterfaceIndex
```

例如：

```text
InterfaceIndex : 12
```

然后使用管理员 PowerShell：

```powershell
Set-NetConnectionProfile -InterfaceIndex 12 -NetworkCategory Private
```

再次检查：

```powershell
Get-NetConnectionProfile
```

确认：

```text
NetworkCategory : Private
```

今天已经成功完成这个修改。

---

# 十二、最终测试成功

修改网络类型后重新测试：

```text
手机
 ↓
Wi-Fi
 ↓
电脑局域网 IP
 ↓
7897
 ↓
Clash Verge
 ↓
代理节点
 ↓
Internet
```

最终：

> **手机已经可以正常通过电脑的 Clash 上网。**

这意味着今天的核心目标已经完成。

---

# 十三、手机端应该怎么设置

手机连接与电脑相同的局域网 / Wi-Fi。

Wi-Fi → 代理 → 手动。

填写：

```text
代理服务器：
电脑的局域网 IPv4

端口：
7897
```

例如：

```text
服务器：192.168.1.105
端口：7897
```

不要填写：

```text
127.0.0.1
```

也不要填写：

```text
localhost
```

因为手机上的 `127.0.0.1` 指的是：

> 手机自己

而不是电脑。

---

# 十四、如何确认手机真的经过 Clash

可以在手机访问一个需要代理的网站。

同时打开电脑 Clash Verge 的：

```text
连接
```

观察连接记录。

如果出现类似：

```text
Google
    ↓
日本专线03
```

说明手机的连接已经进入 Clash，并且通过日本节点访问。

如果看到：

```text
国内网站
    ↓
DIRECT
```

则说明该连接按照 Clash 规则直接访问。

---

# 十五、今天最重要的排障思路

今天实际上学到的不只是“怎么设置 Clash”，更重要的是网络故障排查方法。

以后遇到：

> 手机无法通过电脑代理

不要一上来就乱改 Clash 节点。

应该按照网络链路逐层排查：

```text
① 手机
   ↓
② Wi-Fi / 局域网
   ↓
③ 电脑局域网 IP
   ↓
④ Windows 防火墙
   ↓
⑤ Clash 7897 监听
   ↓
⑥ Clash 接收到连接
   ↓
⑦ Clash 规则
   ↓
⑧ 代理节点
   ↓
⑨ Internet
```

哪个环节断了，就排查哪个环节。

---

# 十六、几个非常重要的命令

## 查看 IP

```powershell
ipconfig
```

---

## 查看网络类型

```powershell
Get-NetConnectionProfile
```

---

## 查看网络接口配置

```powershell
Get-NetIPConfiguration
```

---

## 查看 7897 是否监听

```powershell
netstat -ano | findstr :7897
```

或者：

```powershell
Get-NetTCPConnection -LocalPort 7897
```

---

## 查看防火墙规则

```powershell
Get-NetFirewallRule -DisplayName "Clash Verge 7897 LAN"
```

---

## 查看防火墙端口过滤

```powershell
Get-NetFirewallRule -DisplayName "Clash Verge 7897 LAN" | Get-NetFirewallPortFilter
```

---

# 十七、以后可能再次遇到的问题

## 1. 换 Wi-Fi 后手机又不能用了

首先检查电脑 IP：

```powershell
ipconfig
```

电脑 IP 可能从：

```text
192.168.1.105
```

变成：

```text
192.168.1.108
```

那么手机代理服务器也要修改。

---

## 2. 手机和电脑不是同一个局域网

必须保证：

```text
手机 ──┐
       ├── 同一个局域网 / 路由器
电脑 ──┘
```

如果手机使用的是另一个 Wi-Fi、移动数据或不同的访客网络，就可能无法访问电脑。

---

## 3. 路由器开启客户端隔离

有些路由器存在：

```text
AP Isolation
Client Isolation
Wireless Isolation
客户端隔离
无线隔离
```

开启后：

```text
手机 → Internet     ✅
电脑 → Internet     ✅

手机 → 电脑          ❌
```

这种情况下，即使两台设备连接同一个 Wi-Fi，也可能互相访问不了。

---

# 十八、今天的核心知识总结

### 知识 1：127.0.0.1

```text
127.0.0.1
```

永远表示：

> 当前设备自己。

所以手机不能把：

```text
127.0.0.1:7897
```

当成电脑的代理。

---

### 知识 2：0.0.0.0

```text
0.0.0.0:7897
```

表示程序在所有 IPv4 网络接口上监听。

这与：

```text
127.0.0.1:7897
```

是不同概念。

---

### 知识 3：端口

可以把 IP 想象成：

> 一栋房子的地址

端口可以理解成：

> 房子里的不同入口。

例如：

```text
192.168.1.105:7897
```

就是：

```text
电脑地址 + Clash 代理入口
```

---

### 知识 4：防火墙

即使程序正在：

```text
0.0.0.0:7897
```

监听，Windows 防火墙也可能拒绝外部设备访问。

所以：

```text
程序监听
```

和：

```text
防火墙允许
```

是两个不同的问题。

---

### 知识 5：Private / Public

Windows 的网络类型会影响网络访问策略。

一般家庭局域网适合：

```text
Private
```

公共场所网络通常应保持：

```text
Public
```

不要为了方便就把不可信的公共网络随便设置成 Private。

---

# 十九、今天最终配置

```text
Windows 网络：
Private

Clash Verge：
局域网连接：开启
Mixed Port：7897
系统代理：开启

Windows Firewall：
允许 LocalSubnet → TCP 7897

手机 Wi-Fi：
代理：手动
服务器：电脑局域网 IPv4
端口：7897
```

最终状态：

```text
             ┌── DIRECT ──→ 国内网站
             │
手机 → 电脑 → Clash
             │
             └── 日本节点 ─→ 代理网站
```

---

# 二十、今天真正应该记住的一句话

> **网络排障不是“猜哪里坏了”，而是沿着数据包实际经过的路径，一层一层确认。**

今天从：

```text
手机不能上网
```

最终定位并解决为：

```text
7897 已监听
→ 防火墙规则
→ Windows 网络类型 Public
→ 改为 Private
→ 手机重新连接
→ 成功通过电脑 Clash 上网
```

这套排查思路以后不仅可以用于 Clash，也可以用于理解：

- TCP/IP
- IP 地址
- 端口
- 防火墙
- NAT
- 局域网
- 路由器
- 代理
- VPN
- TUN
- 网络故障排查

它本身就是一次很典型的计算机网络实践。
