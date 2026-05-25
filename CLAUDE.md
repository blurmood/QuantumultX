# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库概述

这是一个 QuantumultX（iOS 网络代理工具）的个人配置仓库，基于墨鱼（@ddgksf2013）的配置模板修改。主要功能包括：
- 广告拦截和应用净化
- VIP 功能解锁（Spotify、哔哩哔哩、Soul 等）
- 智能分流和节点选择
- 流媒体解锁

## 核心文件结构

- `QuantumlutX.conf` - 主配置文件，包含所有 QuantumultX 设置
- `rewrite/` - 自定义重写脚本目录
  - `SoulSuperVipCrac.js` - Soul 应用 VIP 破解脚本

## 配置文件架构

`QuantumlutX.conf` 采用分段式配置，每个部分用 `[section_name]` 标识：

### 关键配置段

1. **[general]** - 基础设置
   - 节点延迟测试 URL 和超时时间
   - GeoIP 数据库配置
   - DNS 排除列表

2. **[server_local] / [server_remote]** - 代理服务器
   - `server_local`: 手动添加的服务器节点
   - `server_remote`: 订阅链接，自动更新节点列表

3. **[policy]** - 策略组
   - 定义节点选择策略（自动选择、手动选择、负载均衡等）
   - 使用 `server-tag-regex` 正则表达式过滤节点
   - 策略类型：`static`（手动）、`url-latency-benchmark`（自动测速）

4. **[filter_local] / [filter_remote]** - 分流规则
   - 决定哪些流量走代理，哪些直连
   - `force-policy` 指定该规则使用的策略组

5. **[rewrite_local] / [rewrite_remote]** - 重写规则
   - 修改 HTTP(S) 请求/响应
   - 用于去广告、解锁 VIP 等功能
   - `opt-parser=true` 表示需要解析器转换格式

6. **[mitm]** - HTTPS 解密
   - 包含证书信息（passphrase 和 p12）
   - `hostname` 列表指定需要解密的域名

## 常见修改操作

### 添加新的代理服务器

在 `[server_local]` 段添加：
```
shadowsocks=server:port, method=encryption, password=pwd, tag=节点名称
```

### 添加自定义分流规则

在 `[filter_local]` 段添加：
```
host-suffix, example.com, proxy    # 域名后缀匹配
host-keyword, keyword, direct      # 域名关键词匹配
ip-cidr, 1.2.3.0/24, reject       # IP 段匹配
```

### 添加自定义重写脚本

1. 将脚本文件放入 `rewrite/` 目录
2. 在 `[rewrite_remote]` 段添加引用：
```
https://raw.githubusercontent.com/blurmood/QuantumultX/refs/heads/main/rewrite/YourScript.js, tag=描述, update-interval=172800, opt-parser=true, enabled=true
```

### 修改策略组节点选择

策略组使用正则表达式过滤节点：
- `server-tag-regex=(?=.*(香港|HK))` - 匹配包含"香港"或"HK"的节点
- `^((?!(港|台|日)).)*$` - 排除包含"港台日"的节点

## Git 工作流

```bash
# 修改配置后提交
git add QuantumlutX.conf
git commit -m "描述修改内容"
git push origin main

# 添加新脚本
git add rewrite/NewScript.js
git commit -m "添加 NewScript 重写脚本"
git push origin main
```

## 注意事项

- **证书信息敏感**：`[mitm]` 段的 `passphrase` 和 `p12` 是个人证书，不要分享
- **服务器信息私密**：`[server_local]` 中的服务器密码应保密
- **订阅链接更新**：`[server_remote]` 中的订阅链接会自动更新，`update-interval` 单位为秒
- **正则表达式测试**：修改策略组的 `server-tag-regex` 前建议先测试正则表达式
- **重写规则顺序**：规则从上到下匹配，优先级递减

## 配置同步

QuantumultX 可以通过以下方式使用此配置：
1. 在 QuantumultX 中点击右下角图标
2. 选择"配置文件" → "下载"
3. 输入配置文件的 raw URL：
   `https://raw.githubusercontent.com/blurmood/QuantumultX/main/QuantumlutX.conf`
