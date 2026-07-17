# zgovps性能测评全攻略：CPU跑分、Speedtest速度、三网路由实测详解——香港/洛杉矶/日本/德国四地机房UnixBench与套餐选购一篇搞定（附最新优惠码与全套餐对比表）

很多人在挑海外 VPS 的时候都会卡在同一个问题上——参数表看得眼花缭乱，但真的想知道一台机器跑起来到底怎么样，要么得自己花钱实测，要么只能满世界翻别人写过的测评。zgovps（也叫 ZgoCloud）这两年在中文圈里被反复提及，主打"AMD EPYC + Intel Xeon Platinum + NVMe + 中国优化线路"这套组合拳，套餐横跨香港、洛杉矶、东京、大阪、德国 Falkenstein 五个机房。如果你正在搜"zgovps性能测评"想搞清楚它家的 EPYC 7003 到底快不快、CMIN2 线路回国稳不稳、哪个套餐适合建站、哪个适合跑流媒体——那这篇就是为你准备的。我把能找到的公开实测数据、官方套餐配置和当前还在生效的优惠码都整理在了一起，按机房分组对比，看完应该能心里有数。

## 一、先认识一下 zgovps：谁在做、用什么硬件

zgovps 的运营主体是 ZgoShop, Inc.，注册在美国特拉华州，备案号 6298021，自己持有 AS197767 这个网络号，是 ARIN 和 RIPE 的正式成员，上游对接 NTT、Orange S.A.、Cogent 这些 Tier 1 运营商。说人话就是：它不是租别人家 IP 转售的小作坊，而是真正自己跑网络、能控路由走向的商家，这也是它能强行把三网回国都塞进 CMIN2/9929/CN2 GIA 的底气所在。

数据中心分布在五个城市：洛杉矶（Equinix）、香港、东京、大阪、德国 Falkenstein，全部采用 1+1 冗余电源、RAID1 阵列、异地灾备。硬件阵容相当豪华，覆盖了当下 VPS 圈几乎能拿到的顶配：

- **AMD EPYC 7002 系列**（Milan，7nm，常见于香港 BGP、洛杉矶 CN2 GIA 三网优化、洛杉矶双 ISP、洛杉矶 Global）
- **AMD EPYC 7003 系列**（Milan-X，常用于洛杉矶 9929+CMIN2 高端线、洛杉矶 VDS）
- **AMD EPYC 9354P**（Genoa，4nm，PCIe 5.0/DDR5，大阪 Performance 系列）
- **AMD Ryzen 9 7950X**（Zen 4，5nm，桌面旗舰 U，洛杉矶和大阪都有 Ryzen9 系列）
- **Intel Xeon Platinum 8452Y**（Sapphire Rapids，DDR5 + PCIe 4.0，洛杉矶 Intel Performance）
- **Intel Xeon Gold 6248**（Cascade Lake，东京 Intel 系列）
- **Intel Xeon Gold 5412U**（Sapphire Rapids，DDR5，德国 Falkenstein）

全部 NVMe SSD，部分高端套餐用 PCIe 4.0 NVMe；DDR4 和 DDR5 混搭，越新的机型越倾向 DDR5。底层一律 KVM 虚拟化，VirtFusion 管理面板。支付支持 PayPal、Stripe、信用卡、支付宝，国内用户付款基本没障碍。

## 二、性能实测数据：从 CPU 跑分到晚高峰带宽

测评数据来自主机测评、VPS 小学生、VPS 加油站、tashcp、idccoupon、ecscoupon 等多个第三方站点的实测记录，按机房归类整理。

**CPU 与 IO 跑分**

洛杉矶 AMD EPYC 7003（CMIN2 高端线）在 Geekbench 6 里单核跑出 1700+，这个分数在中小厂 VPS 里属于第一梯队，跟 DMIT 的 7402P 打个平手，明显高于 Global 国际线用的 EPYC 7282P（GB6 单核约 1194）。UnixBench 综合跑分方面，双 ISP 系列用的 EPYC 7452（1 核 2.345GHz）单核分数在 1300 上下，多核线性扩展，跑个人博客、小型论坛、AI 反代、WordPress 建站这类负载完全够用。

大阪 EPYC 9354P 凭借 Genoa 新架构 + DDR5，单核性能比 EPYC 7003 还要再高一档，适合对 CPU 敏感的场景（比如编译、转码）。Ryzen 9 7950X 则是单核怪兽，主频能拉到 5.7GHz，Geekbench 6 单核破 2000 没压力，WordPress 首屏加载、PHP-FPM 响应比 EPYC 还快一截，缺点是核心数少、价格更贵。

磁盘 IO 方面，NVMe 阵列在大文件顺序读写能跑到 1GB/s 以上，4K 随机也能维持几万 IOPS，IO 不会是瓶颈。

**网络测速**

香港 BGP：三网各自直连，电信去程走 CN2、联通走 AS9929、移动走 CMI，看视频实测 22 万+ KB/s（约 215MB/s），晚高峰也基本不掉速，国内延迟 30–60ms，是 zgovps 里国内访问体验最丝滑的机房。

洛杉矶 CMIN2（AMD EPYC 7003）：三网强制走移动 CMIN2，1Gbps 入口带宽，下载测速 13 万+ KB/s，晚高峰仍能维持满速大半，回程加速网络效果明显，延迟 150ms 左右。

洛杉矶双 ISP（EPYC 7002 + 9929+CMIN2）：自带双 ISP 住宅属性 IP（数据中心托管，不是真的家用宽带 IP，除了 IP2Location 之外多数库都识别为双 ISP），适合需要 IP 干净度的场景（比如解锁某些对 IDC IP 敏感的服务），电信/联通走 9929，移动走 CMIN2，速度和延迟跟纯 CMIN2 同档。

东京 Intel Gold 6248（BGP 优化）：看视频 18 万+ KB/s，三网往返直连，延迟比洛杉矶低不少（华东 50–80ms），缺点是只有 100Mbps 带宽、流量偏小，且经常缺货。

大阪 EPYC 9354P（IIJ 线路）：IIJ 是日本主流商业线路，国内访问延迟比东京略高，但带宽给得大方（400–800Mbps），适合日本本地业务或亚太分发。

德国 Falkenstein（Intel Gold 5412U + 1Gbps）：欧洲节点，国内访问绕路严重、延迟 200ms+，不适合做国内优化用途，但适合欧洲本地用户、跨境出海业务、或者拿来挂 PT、做轻量代理中转，价格便宜到夸张（年付 $12.9 起）。

## 三、流媒体与 AI 解锁实测

zgovps 各机房在解锁方面口碑不错，多个测评记录显示：

- **香港 BGP**：可解锁 TikTok（美区）、ChatGPT、Gemini、Claude、Netflix、Disney+、Steam 美区定价
- **洛杉矶 CMIN2/双 ISP**：原生美国 IP，解锁 TikTok 美区、ChatGPT、Netflix、Disney+、Gemini、Claude、Steam 美区
- **东京 Intel**：可解锁日本区 TikTok、ChatGPT、Gemini、Claude、Netflix、Disney+、Reddit、Steam 日区定价
- **大阪 EPYC/Ryzen9**：IIJ 线路，日本区解锁能力同东京档

需要提醒的是，洛杉矶双 ISP 系列的 IP 是数据中心托管的双 ISP 属性，官方明确说明"Except IP2Location, all databases identify them as dual ISP"，介意 IP 真实住宅属性的话不要买这款，也不支持以此理由退款。

## 四、全套餐对比表（按机房分组）

下面是 zgovps 官网在售的全部套餐，按机房归类。AFF 链接已用 `affid=1247` 拼接对应商品 ID 生成专属购买地址，点击即可直达对应套餐下单页。

### 4.1 香港 AMD VPS（BGP 三网各自直连）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Specials - Lite | 1×EPYC 7002 | 512MB DDR4 | 10GB | 300GB | 100Mbps | $36.8/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=123) |
| Specials - Starter | 1×EPYC 7002 | 1GB DDR4 | 10GB | 500GB | 100Mbps | $45/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=121) |
| Specials - Standard | 2×EPYC 7002 | 2GB DDR4 | 20GB | 1TB | 100Mbps | $96/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=122) |
| Starter | 1×EPYC 7002 | 1GB DDR4 | 10GB | 500GB | 100Mbps | $16/季（$66/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=117) |
| Standard | 2×EPYC 7002 | 2GB DDR4 | 20GB | 1TB | 100Mbps | $30/季（$116/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=118) |
| Pro | 3×EPYC 7002 | 3GB DDR4 | 30GB | 1.5TB | 100Mbps | $45/季（$156/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=119) |
| Premium | 4×EPYC 7002 | 4GB DDR4 | 50GB | 2TB | 100Mbps | $58/季（$198/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=120) |

### 4.2 洛杉矶 AMD Optimised VPS（CN2 GIA + 9929 + CMIN2 三网纯高端）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Specials - Lite | 1×EPYC 7002 | 512MB DDR4 | 10GB | 300GB | 200Mbps | $52/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=134) |
| Specials - Standard | 2×EPYC 7002 | 2GB DDR4 | 20GB | 1TB | 200Mbps | $96/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=136) |
| Starter | 1×EPYC 7002 | 1GB DDR4 | 10GB | 500GB | 200Mbps | $18/季（$66/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=142) |
| Standard | 2×EPYC 7002 | 2GB DDR4 | 20GB | 1TB | 200Mbps | $32/季（$116/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=143) |
| Pro | 3×EPYC 7002 | 3GB DDR4 | 30GB | 1.5TB | 200Mbps | $45/季（$156/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=144) |
| Premium | 4×EPYC 7002 | 4GB DDR4 | 50GB | 2TB | 200Mbps | $58/季（$198/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=145) |

### 4.3 洛杉矶 AMD ISP VPS（双 ISP + 9929 + CMIN2）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Starter | 1×EPYC 7002 | 1GB DDR4 | 10GB | 500GB | 100Mbps | $20/季（$58/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=148) |
| Standard | 2×EPYC 7002 | 2GB DDR4 | 20GB | 1TB | 100Mbps | $38/季（$108/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=149) |
| Pro | 3×EPYC 7002 | 3GB DDR4 | 30GB | 1.5TB | 200Mbps | $56/季 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=150) |
| Premium | 4×EPYC 7002 | 4GB DDR4 | 50GB | 2TB | 200Mbps | $72/季 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=151) |

### 4.4 洛杉矶 AMD VPS（EPYC 7003 + 9929 + CMIN2）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Starter | 1×EPYC 7003 | 2GB DDR4 | 30GB | 1TB | 300Mbps | $16/季（$60/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=68) |
| Standard | 2×EPYC 7003 | 3GB DDR4 | 50GB | 2TB | 300Mbps | $24/季（$90/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=69) |
| Pro | 3×EPYC 7003 | 4GB DDR4 ECC | 80GB PCIe 4.0 | 2TB | 300Mbps | $32/季（$120/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=72) |
| Premium | 4×EPYC 7003 | 6GB DDR4 ECC | 100GB PCIe 4.0 | 2TB | 300Mbps | $40/季（$150/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=73) |
| Ultra | 6×EPYC 7003 | 8GB DDR4 ECC | 120GB PCIe 4.0 | 2TB | 500Mbps | $48/季（$176/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=74) |

### 4.5 洛杉矶 Intel Performance VPS（Platinum 8452Y + 9929 + CMIN2）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Starter | 1×Platinum 8452Y | 1GB DDR5 ECC | 20GB PCIe 4.0 | 1TB | 300Mbps | $16/季（$60/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=26) |
| Standard | 2×Platinum 8452Y | 2GB DDR5 ECC | 40GB PCIe 4.0 | 2TB | 300Mbps | $24/季（$90/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=27) |
| Pro | 3×Platinum 8452Y | 4GB DDR5 ECC | 80GB PCIe 4.0 | 2TB | 300Mbps | $32/季（$120/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=28) |
| Premium | 4×Platinum 8452Y | 6GB DDR5 ECC | 100GB PCIe 4.0 | 2TB | 300Mbps | $40/季（$150/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=29) |

### 4.6 洛杉矶 Ryzen9 Performance VPS（Ryzen 9 7950X + CN2 GIA + 9929 + CMIN2）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Specials - Lite | 1×Ryzen 9 7950X | 512MB DDR5 | 15GB | 500GB | 200Mbps | $38.9/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=101) |
| Specials - Starter | 1×Ryzen 9 7950X | 1GB DDR5 | 25GB | 1TB | 500Mbps | $58.9/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=60) |
| Starter | 1×Ryzen 9 7950X | 1GB DDR5 | 25GB | 1TB | 300Mbps | $18/季（$66/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=58) |
| Standard | 2×Ryzen 9 7950X | 2GB DDR5 | 40GB | 2TB | 500Mbps | $28/季（$106/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=59) |

### 4.7 洛杉矶 Global VPS（EPYC 7002 + 国际线路，不优化中国）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Specials - Lite | 1×EPYC 7002 | 512MB DDR4 | 15GB | 1TB | 1Gbps | $9.9/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=91) |
| Specials - Basic | 1×EPYC 7002 | 768MB DDR4 | 18GB | 1.5TB | 1Gbps | $12.9/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=92) |
| Specials - Starter | 1×EPYC 7002 | 1GB DDR4 | 20GB | 2TB | 1Gbps | $15/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=93) |
| Specials - Standard | 2×EPYC 7002 | 2GB DDR4 | 40GB | 4TB | 1Gbps | $25/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=94) |
| Specials - Pro | 3×EPYC 7002 | 4GB DDR4 | 60GB | 6TB | 1Gbps | $45/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=95) |
| Starter | 1×EPYC 7002 | 1GB DDR4 | 20GB | 2TB | 1Gbps | $8/季（$28/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=84) |
| Standard | 2×EPYC 7002 | 2GB DDR4 | 40GB | 4TB | 1Gbps | $12/季（$40/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=85) |
| Pro | 3×EPYC 7002 | 4GB DDR4 | 60GB | 6TB | 1Gbps | $20/季（$72/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=86) |
| Premium | 4×EPYC 7002 | 6GB DDR4 | 80GB | 8TB | 1Gbps | $28/季（$98/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=87) |

### 4.8 洛杉矶 AMD VDS（EPYC 7003 + 国际线路，可装 Windows，独享资源）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Specials - Starter | 2×EPYC 7003 | 4GB DDR4 | 60GB | 10TB | 1Gbps | $66/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=125) |
| Specials - Standard | 4×EPYC 7003 | 8GB DDR4 | 150GB | 20TB | 1Gbps | $96/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=106) |
| Specials - Pro | 8×EPYC 7003 | 16GB DDR4 | 250GB | 20TB | 2Gbps | $166/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=107) |
| Specials - Premium | 12×EPYC 7003 | 24GB DDR4 | 500GB | 20TB | 2Gbps | $258/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=108) |
| Starter | 2×EPYC 7003 | 4GB DDR4 | 60GB | 10TB | 1Gbps | $24/季 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=124) |
| Standard | 4×EPYC 7003 | 8GB DDR4 | 150GB | 20TB | 1Gbps | $32/季 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=103) |
| Pro | 8×EPYC 7003 | 16GB DDR4 | 250GB | 20TB | 2Gbps | $52/季 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=104) |
| Premium | 12×EPYC 7003 | 24GB DDR4 | 500GB | 20TB | 2Gbps | $76/季 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=105) |

### 4.9 大阪 AMD Performance VPS（EPYC 9354P + IIJ）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Starter | 1×EPYC 9354P | 1GB DDR5 ECC | 20GB PCIe 4.0 | 1TB | 400Mbps | $12/季（$52/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=11) |
| Standard | 2×EPYC 9354P | 2GB DDR5 ECC | 40GB PCIe 4.0 | 2TB | 800Mbps | $17/季（$66/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=12) |
| Pro | 3×EPYC 9354P | 4GB DDR5 ECC | 80GB PCIe 4.0 | 2TB | 800Mbps | $24/季（$96/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=13) |
| Premium | 4×EPYC 9354P | 6GB DDR5 ECC | 100GB PCIe 4.0 | 2TB | 800Mbps | $36/季（$144/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=14) |
| Ultra | 6×EPYC 9354P | 8GB DDR5 ECC | 120GB PCIe 4.0 | 2TB | 800Mbps | $48/季（$192/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=15) |

### 4.10 大阪 AMD Ryzen9 Performance VPS（Ryzen 9 7950X + IIJ）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Specials - Lite | 1×Ryzen 9 7950X | 512MB DDR5 | 15GB | 700GB | 400Mbps | $28/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=19) |
| Specials - Starter | 1×Ryzen 9 7950X | 1GB DDR5 | 20GB | 1TB | 800Mbps | $38/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=20) |
| Starter | 1×Ryzen 9 7950X | 1GB DDR5 ECC | 20GB PCIe 4.0 | 1TB | 800Mbps | $15/季（$52/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=18) |
| Standard | 2×Ryzen 9 7950X | 2GB DDR5 ECC | 40GB PCIe 4.0 | 2TB | 800Mbps | $25/季（$92/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=21) |

### 4.11 东京 Intel VPS（Xeon Gold 6248 + BGP 中国优化）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Specials - Starter | 1×Gold 6248 | 1GB DDR4 | 10GB | 500GB | 100Mbps | $45/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=132) |
| Specials - Standard | 2×Gold 6248 | 2GB DDR4 | 20GB | 1TB | 100Mbps | $88/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=133) |
| Starter | 1×Gold 6248 | 1GB DDR4 | 10GB | 500GB | 100Mbps | $16/季（$66/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=127) |
| Standard | 2×Gold 6248 | 2GB DDR4 | 20GB | 1TB | 100Mbps | $30/季（$116/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=128) |
| Pro | 3×Gold 6248 | 3GB DDR4 | 30GB | 1.5TB | 100Mbps | $45/季（$156/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=129) |
| Premium | 4×Gold 6248 | 4GB DDR4 | 50GB | 2TB | 100Mbps | $58/季（$198/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=130) |

### 4.12 德国 Falkenstein Intel VPS（Xeon Gold 5412U + 国际线路）

| 套餐 | CPU | 内存 | NVMe | 流量/月 | 带宽 | 价格 | 购买 |
|------|-----|------|------|---------|------|------|------|
| Starter | 1×Gold 5412U | 1GB DDR5 ECC | 20GB | 2TB | 1Gbps | $6/季（$22.9/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=49) |
| Standard | 2×Gold 5412U | 2GB DDR5 ECC | 40GB | 4TB | 1Gbps | $10/季（$39.9/年） | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=50) |
| Starter（年付特价） | 1×Gold 5412U | 1GB DDR5 ECC | 20GB | 2TB | 1Gbps | $12.9/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=53) |
| Standard（年付特价） | 2×Gold 5412U | 2GB DDR5 ECC | 40GB | 4TB | 1Gbps | $22.9/年 | [购买](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=54) |

## 五、最新优惠码与促销活动

zgovps 目前公开的主推优惠码如下，**注意所有 Specials 系列特价套餐本身已经不能叠加优惠码**，优惠码主要适用于常规正价款：

| 优惠码 | 折扣 | 适用范围 | 计费周期 | 有效期 |
|--------|------|----------|----------|--------|
| `8NU44CM6LZ` | 9.5 折循环（续费同享） | 洛杉矶/大阪常规 VPS | 仅年付 | 2026 年 12 月 31 日 |
| `BPZZ1GE8T7` | 8.5 折 | 部分常规套餐 | — | 需下单时实测 |

下单时在购物车页面找到 "Use promotional code" 输入框提交即可。建议买之前先把目标套餐加入购物车，分别试一下两个码，看哪个折扣力度更大。

> 提醒一句：zgovps 后台开了 WHMCS MaxMind 自动反欺诈，下单时 IP 地址、电话国家代码、所选国家这三项必须保持一致（不要求信息真实，但要一致），否则会被判 Fraud 拦下来。国内用户下单前最好挂个对应机房的代理再提交订单。

需要进官方商店浏览所有在售方案的话，可以直接走 👉 [ZgoCloud 商店首页](https://bit.ly/zgovps) 逛一圈。

## 六、按使用场景的选购建议

**1. 想要国内访问最丝滑、追求低延迟**

首选 👉 [香港 BGP Standard](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=118)（2C/2G/20G/1TB/100Mbps，$30/季），三网各自直连，延迟 30–60ms，看视频、远程桌面、SSH 都跟国内服务器没差多少。预算紧张就上 [Specials - Starter](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=121)（$45/年）。

**2. 想要美国原生 IP + 大带宽 + 三网优化**

[洛杉矶 AMD VPS - Standard](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=69)（2C/3G/50GB/2TB/300Mbps，$24/季）是性价比甜点，EPYC 7003 + DDR4 + 9929/CMIN2，跑 WordPress、AI 反代、流媒体解锁都游刃有余。想要 CN2 GIA 三网纯高端加成就上 [洛杉矶 AMD Optimised - Standard](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=143)。

**3. 想要双 ISP 干净 IP 解锁敏感服务**

[洛杉矶 AMD ISP - Standard](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=149)，IP 是数据中心托管的双 ISP 属性，绝大多数库都识别为 ISP 而非 IDC，适合跑 ChatGPT、TikTok 美区、Netflix 这类对 IP 类型敏感的应用。

**4. 想要极致单核性能（编译、PHP、游戏服务器）**

[洛杉矶 Ryzen9 - Standard](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=59) 或者 [大阪 Ryzen9 - Standard](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=21)，Ryzen 9 7950X 主频 5.7GHz，单核性能吊打所有 EPYC。

**5. 想要跑 Windows、需要独享资源**

[洛杉矶 VDS - Specials Standard](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=106)（4C/8G/150GB/20TB/1Gbps，$96/年），可自备 Windows 授权安装，独享 CPU 不超售，跑远程桌面、ERP、生产环境都很合适。

**6. 预算极低、只想跑个轻量任务**

[洛杉矶 Global - Specials Lite](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=91)（512MB/15GB/1TB/1Gbps，$9.9/年）或者 [德国 Falkenstein Starter 特价](https://clients.zgovps.com/?cmd=cart&action=add&affid=1247&id=53)（1G/20GB/2TB/1Gbps，$12.9/年），都是国际线路不优化中国，但胜在量大管饱，挂个探针、做中转、跑个小博客足够了。

## 七、总结

回头看完整测评和套餐表，zgovps 的定位其实很清晰：用一二线大厂才舍得堆的硬件（EPYC 7003/9354P、Ryzen 9 7950X、Xeon Platinum 8452Y），配上自有的 AS197767 网络强行塞 CMIN2/9929/CN2 GIA，把"高性能 + 中国优化"这件事做到了中小厂里偏头部的位置。香港和洛杉矶 CMIN2 这两条线在国内访问体验上确实属于第一梯队， Osaka 的 EPYC 9354P + IIJ 组合在亚太分发场景也很能打，德国 Falkenstein 则是纯粹的低价欧洲节点，定位互补。

如果你之前一直在纠结"zgovps 性能到底怎么样、值不值得买"，看完这份按机房分组的实测数据 + 全套餐对比表 + 当前有效的优惠码，应该能比较快对号入座找到适合自己的那一款。下单记得先用 `8NU44CM6LZ` 试一下 9.5 折，Specials 特价款就不要折腾优惠码了直接拍。有问题可以直接走 👉 [ZgoCloud 商店](https://bit.ly/zgovps) 提工单问客服，Telegram 频道也有官方人员驻场。
