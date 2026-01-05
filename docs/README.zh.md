<p align="center">
  <strong>-------></strong>
  <a href="/README.md">俄文</a> |
  <a href="/docs/README.en.md">英文</a> |
  <a href="/docs/README.es.md">西班牙文</a> |
  <a href="/docs/README.zh.md">中文</a> |
  <strong><-------</strong>
</p>

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="../media/logo-dark.png">
    <img alt="工程Logo" src="../media/logo-light.png" width="512" height="auto">
  </picture>
</p>

---

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-blue?style=flat&logo=github)](https://github.com/AnikBeris)
[![License](https://img.shields.io/badge/License-purple?style=flat&logo=github)](/LICENSE.md)
[![GitHub Stars](https://img.shields.io/github/stars/AnikBeris?style=flat&logo=github&label=Stars&color=orange)](https://github.com/AnikBeris)

</div>

<div align="center">
  <img src="../media/image0.gif" alt="SPACER" width="90%">
</div>

<h1 align="center"> 
Unreal Engine 5.7 – 共享派生数据缓存 (DDC) 和内容存储
</h1>

<h2 align="center">
> 💡 本文档描述了如何使用 Docker 和 MinIO 为 Unreal Engine 5.7 设置 **共享派生数据缓存 (DDC)** 和 **内容存储** 的架构、用途及实际实现。此文档适用于个人开发者和小型团队，并可扩展至 CI/CD 项目。

</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 

<h2 align="center">
⚠️免责声明⚠️
</h2>

<p align="center">
  作者对使用本项目可能导致的任何后果概不负责。<br>
  请在自担风险的前提下使用。
</p>

<details align="center">
    <summary>⚠️完整声明⚠️</summary>
    
使用此存储库中的资料需自担风险。

1. 使用此存储库的资料，表示您已自动同意与之相关的许可协议条款。

2. 作者不对这些资料的准确性、完整性或适用于任何特定目的的适用性提供任何明确或暗示的保证。

3. 因使用或无法使用此存储库中的资料或随附文档而导致的任何损失，包括但不限于直接、间接、附带性、间接性或特殊损失，即使已事先告知可能发生这些损失，作者概不负责。

4. 使用此存储库中的资料，表示您承认并承担与其应用相关的所有风险。此外，您同意作者因为使用本资料所产生的任何问题或后果不承担任何责任。

</details> 

---

<h3 align="center"> 
💖 支持项目 
</h3>

<p align="center"> 
如果此项目对您有用，可以通过点赞表达支持。:star2: 
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
任何形式的捐赠都是欢迎的，哪怕只是小额支持，非常感谢。😌 
</h4>

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
  <sub> 感谢您关注和支持项目 💙 </sub>
</p>


</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
  📖 Unreal Engine 5.7 缓存设置指南
</h2>

## 📚 目录

- [🎯 简介 - 为什么需要缓存](#-vvedenie)
- [💾 缓存是什么以及存储了什么](#1-chto-imenno-keshiruyetsya-v-unreal-engine)
- [🏗️ 缓存系统架构](#2-arkhitektura-ddc-v-ue-57)
- [📁 基于共享文件夹的简单设置](#3-shared-ddc-cherez-filesystem)
- [☁️ 基于 MinIO 的高级设置（推荐方案）](#4-shared-ddc-cherez-minio-rekomendovannyy-variant)
- [🤖 自动化与 CI/CD](#5-ddc-i-cicd-kriticheski-vazhno)
- [📦 项目文件存储](#6-content-storage-ne-tolko-ddc)
- [🔢 缓存版本管理 - 常见问题及规则](#7-versiionirovaniye-kesha-chastaya-oshibka)
- [📊 文件监控与清理](#8-monitoring-i-ochistka)
- [⚠️ 不该缓存的内容](#9-chto-ne-stoit-keshiruyet)
- [📈 加速的实际数据](#10-rezultat-v-cifrakh)
- [🛠️ 基础工具建议](#11-rekomenduemyy-minimalnyy-stek)
- [📚 补充材料](#dopolnitelno)

---

## 🔗 实用链接

**官方文档:**
- [Unreal Engine 文档](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**工具:**
- [MinIO 官方网站](https://min.io/)
- [Docker 文档](https://docs.docker.com/)

**社区:**
- [Unreal Engine 社区](https://forums.unrealengine.com/)
- [Unreal Slackers Discord](https://unrealslackers.org/)

---

<h2 align="center">
  <a href="#-soderzhanie">⬆️返回目录</a>
</h2>

<h2 align="center">
  <a href="#-soderzhanie">⬆️顶部</a>
</h2>

---

## 4️⃣ 基于 MinIO 的 Shared DDC 设置（推荐方案）

### 4.1 什么是 MinIO，为什么需要？

**简单来说** `MinIO` 是一种软件，可以将磁盘上的普通文件夹变成云存储（类似 AWS S3，但为自建）。

<details> 
    <summary>⚙️ 展开描述</summary>

**类比概念:**
- **Filesystem** = 电脑上的普通文件夹
- **MinIO** = 同样的文件夹，但带有 Web 界面和互联网访问功能

**为什么 MinIO 优于普通文件夹？**

| 功能 | 普通文件夹 | MinIO |
|---------|---------------|-------|
| 局域网使用 | ✅ 是 | ✅ 是 |
| 互联网使用 | ❌ 比较复杂 | ✅ 简单 |
| 文件版本管理 | ❌ 无 | ✅ 支持 |
| 自动清理旧文件 | ❌ 手动 | ✅ 自动化 |
| 使用监控 | ❌ 无 | ✅ 有 |
| 扩展性 | ❌ 较差 | ✅ 优秀 |
| 安全性 | ⚠️ 基础的 | ✅ 高级 |

</details>

<h2 align="center">
  <a href="#-soderzhanie">⬆️返回目录</a>
</h2>


