<p align="center">
  <strong>-------></strong>
  <a href="/README.md">Русский</a> |
  <a href="/docs/README.en.md">English</a> |
  <a href="/docs/README.es.md">Spanish</a> |
  <a href="/docs/README.zh.md">Chinese</a> |
  <strong><-------</strong>
</p>



<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="../media/logo-dark.png">
    <img alt="Project Logo" src="../media/logo-light.png" width="512" height="auto">
  </picture>
</p>

---

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-blue?style=flat&logo=github)](https://github.com/AnikBeris)
[![License](https://img.shields.io/badge/License-purple?style=flat&logo=github)](/LICENSE.md)
[![GitHub Stars](https://img.shields.io/github/stars/AnikBeris?style=flat&logo=github&label=Star数&color=orange)](https://github.com/AnikBeris)

</div>

<div align="center">
  <img src="../media/image0.gif" alt="SPACER" width="90%">
</div>


<h1 align="center"> 
Unreal Engine 5.7 – Shared Derived Data Cache (DDC) 和 Content Storage
</h1>

<h2 align="center">
  
> 💡 本文面向独立开发者和小团队，同时可拓展至 CI/CD 和中型工作室。

> 💡 本文档描述了如何利用 Docker 和 MinIO 构建 Unshared Derived Data Cache (DDC) 和 Content Storage 的架构、目标及其实际实现。

</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 




<h2 align="center">
⚠️ 免责声明 ⚠️
</h2>

<p align="center">
  作者不对使用本项目可能带来的任何后果负责。<br>
  使用时须自行承担风险。
</p>

<details align="center"> 
    <summary>⚠️完整内容⚠️</summary>
    
请在了解风险的情况下使用本仓库内容。

1. 使用本仓库内容时，您即同意与本仓库内容相关的许可协议条款。

2. 作者对这些材料的准确性、完整性或适用于任何特定目的不作任何明示或暗示的保证。 
   
3. 作者不对因使用或无法使用本仓库内容或相关文档导致的任何损失负责，包括但不限于直接、间接、附带、从属或特殊损失，即使这些可能的损失已经事先被告知。

4. 使用本仓库内容时，您确认并接受使用所关联的一切风险。此外，您同意作者不对因使用这些内容而产生的任何问题或后果负责。

</details> 

---

<h3 align="center"> 
💖 支持项目 
</h3>

<p align="center"> 
如果您觉得本项目对您有帮助，请给它打个星星吧。:star2: 
</p>


<details align="center"> 
    <summary>💖支持项目💖</summary>

<p align="center">
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="Buy Me a Coffee">
  </a>
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="Buy Me a Coffee">
  </a>
</p>



<h4 align="center"> 
无论金额多少，捐赠都受到热烈欢迎，感谢您！ 😌 
</h1>

<div align="center">

|  |  |
|-------------:|:-------------|
| **Tether USDT (BEP20)** |`0x22258ea591966e830199d27dea7c542f31ed5dc5`|
| **Bitcoin (BTC)** |`1Dbwq9EP8YpF3SrLgag2EQwGASMSGLADbh`|
| **Ethereum (ERC20)** | `0x22258ea591966e830199d27dea7c542f31ed5dc5`|
| **Binance Smart Chain (BEP20)** | `0x22258ea591966e830199d27dea7c542f31ed5dc5`|
| **Solana (SOL)** | `yYYXsiVTzsvfvsMnBxfxSZEWTGytjAViE2ojf3hbLeF`|

</div>

---

<p align="center">
  <sub> 感谢您关注和支持本项目 💙 </sub>
</p>


</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
  <a href="#-内容">📖 Unreal Engine 5.7 缓存配置指南</a>
</h2>

## 📚 内容

- [🎯 前言 - 为什么需要它](#-前言)
- [💾 缓存是什么及其存储内容](#1-unreal-engine-缓存的具体内容)
- [🏗️ 缓存系统如何构造](#2-ue-57中ddc的架构)
- [📁 基于共享文件夹的简单配置](#3-通过filesystem配置共享ddc)
- [☁️ 基于MinIO的高级配置（推荐）](#4-通过minio配置shared-ddc推荐配置)
- [🤖 自动化和CI/CD](#5-ddc和cicd关键重要性)
- [📦 项目其他文件存储](#6-content-storage不止于ddc)
- [🔢 缓存版本 - 重要规则](#7-缓存版本化常见错误)
- [📊 监控与清理过期文件](#8-监控与清理)
- [⚠️ 不需要缓存的内容](#9-什么不应缓存)
- [📈 效率提升数据](#10-结果数据)
- [🛠️ 最少工具配置](#11-推荐最低工具组合)
- [📚 额外资料](#额外信息)

---

## 🔗 实用链接

**官方文档:**
- [Unreal Engine 文档](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**工具:**
- [MinIO 官网](https://min.io/)
- [Docker 文档](https://docs.docker.com/)

**社区:**
- [Unreal Engine 论坛](https://forums.unrealengine.com/)
- [Unreal Slackers Discord](https://unrealslackers.org/)

---

<h2 align="center">
  <a href="#-内容">⬆️ 返回目录</a>
</h2>


<h2 align="center">
  <a href="#-内容">⬆️ 回到顶部</a>
</h2>

---

## 4️⃣ 通过MinIO配置Shared DDC（推荐配置）

### 4.1 MinIO是什么？为何需要它？

**简单来说**，`MinIO` 是一款可以将磁盘上的普通文件夹转化为类似 AWS S3 的云存储的程序。

<details> 
    <summary>⚙️ 展开描述</summary>

**类比：**
- **Filesystem** = 电脑上的普通文件夹
- **MinIO** = 同一个文件夹，但具有网页界面和网络访问功能

**为什么MinIO优于普通文件夹？**

| 特性 | 普通文件夹 | MinIO |
|------|------------|-------|
| 局域网内工作 | ✅ 可以 | ✅ 可以 |
| 互联网访问 | ❌ 困难 | ✅ 简单 |
| 文件版本化 | ❌ 不支持 | ✅ 支持 |
| 自动清理旧文件 | ❌ 手动 | ✅ 自动 |
| 使用情况监控 | ❌ 不支持 | ✅ 支持 |
| 可扩展性 | ❌ 差 | ✅ 优秀 |
| 安全性 | ⚠️ 基础 | ✅ 高级 |

</details>

---

### 4.2 架构原理

<details> 
    <summary>⚙️ 展开描述</summary>

```
开发者（Unreal 编辑器）
         ↓ HTTP 请求
      MinIO 服务器
         ↓
    磁盘上的文件夹
    /minio-data/
      ├─ ue-ddc-5-7/     ← 存放文件的桶（Bucket）
      │   ├─ Win64/
      │   └─ Linux/
```

**什么是Bucket？**
- `Bucket` 是 MinIO 中的虚拟文件夹，相当于用于存储文件的独立“箱子”。

</details>


### 4.3 通过Docker安装MinIO

<details> 
    <summary>⚙️ 展开描述</summary>

#### 为什么使用Docker？

- `Docker` 是将程序以“容器”形式运行在计算机系统中的工具，具有隔离性且不依赖运行环境。

**优势:**
- ✅ 无论是 Windows、Linux 还是 Mac，都能一致运行
- ✅ 不需要安装依赖项
- ✅ 不喜欢可以轻松移除
- ✅ 不会对主系统产生影响

</details>

---


<h2 align="center">
  <a href="#-内容">⬆️ 返回目录</a>
</h2>


