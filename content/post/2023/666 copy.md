---
title: "爱快/OpenWrt/SSRP/ADG DNS分流/一层 NAT 配置"
date: 2023-04-09T03:02:52+08:00
lastmod: 2023-04-09T03:02:52+08:00
description: 路由器折腾记
tags:
  - 路由器
categories:
  - 路由器
image: 
slug: stack-theme-mod

---
{{< quote >}}

摘要： 本文介绍了如何使用软路由配置爱快为主路由，OpenWrt 作为旁路网关，并在OpenWrt中实现DNS去广告和国内外流量分流，同时应用全锥形NAT以便于PCDN运行。文章重点介绍了如何配置双ADG配合SSRP，实现国内外DNS分流，并给出了具体的配置方法。

{{< /quote >}}

<br>

<span class="blur">是高斯模糊呐~ </span>

<br>

<br>
本文探讨软路由配置爱快为主路由，OpenWrt 作为旁路网关的设置。并且OpenWrt 提供 DNS 去广告和国内外分流。整个网络单层 NAT，并设置为全锥形 NAT 便于 PCDN 的运行。
<br>

## 网络环境
- 内网：10.0.0.0/24
- 外网：PPPoE 拨号
- 爱快：10.0.0.1
- OpenWrt: 10.0.0.2
<br>

## 单层 NAT 配置
爱快作为主路由，所以整个内网由爱快提供 NAT。在爱快中开启 NAT1 类型：<img src= 0321233137.png>
爱快开启 UPnP，OpenWrt 关闭 UPnP。
关闭 OpenWrt 全锥形 NAT，关闭防火墙动态伪装。开启转发。

OpenWrt 的动态伪装就是 NAT，开了之后会多一层 NAT，一定要关掉。

关闭动态伪装之后，由于 ARP 验证可能导致无法上网，实测主路由为爱快时，不影响上网。
<br>

## 验证是否单层 NAT
你需要知道：此设置不需要 SmartDNS 和 Turbo ACC 的 DNS 缓存。如有全部关闭。

在 OpenWrt 可以直接安装 luci-app-adguardhome。安装之后进行配置国内版 ADG。

首先设置重定向模式：作为 dnsmasq 上游服务器。

进入 ADG 管理面板后，设置国内 DNS。上游 DNS 服务器设置如下（参考）：
<br>

### 配置 dnsmasq
进入到 OpenWrt 的 「网络」 - 「DHCP/DNS」，DNS 转发应该只有 ADG 的地址。我这边就是 `127.0.0.1#6053`，没有其他转发了。
<br>
