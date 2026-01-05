```html
<p align="center">
  <strong>-------></strong>
  <a href="/README.md">俄语</a> |
  <a href="/docs/README.en.md">英语</a> |
  <a href="/docs/README.es.md">西班牙语</a> |
  <a href="/docs/README.zh.md">中文</a> |
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
[![GitHub Stars](https://img.shields.io/github/stars/AnikBeris?style=flat&logo=github&label=星星&color=orange)](https://github.com/AnikBeris)

</div>

<div align="center">
  <img src="../media/image0.gif" alt="SPACER" width="90%">
</div>

<h1 align="center"> 
Unreal Engine 5.7 – 共享派生数据缓存（DDC）与内容存储 
</h1>

<h2 align="center">
> 💡 此文档描述了 **Shared Derived Data Cache (DDC)** 和 **Content Storage** 在 Unreal Engine 5.7 中的架构、用途和实际实现，适用于个人开发者和小型团队，并具有 CI/CD 的可扩展性。

</h2>

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 

<h2 align="center">
⚠️ 免责声明 ⚠️
</h2>

<p align="center">
  作者对使用此项目可能导致的任何后果概不负责。<br>
  使用需自担风险。
</p>

<details align="center"> 
    <summary>⚠️完整文本⚠️</summary>
    
使用此存储库中的材料需自行承担风险。

1. 使用本存储库中的内容即表示您自动接受与其有关的许可协议条款。

2. 作者对于这些材料的准确性、完整性或是否适用于任何特定目的，不提供任何形式的明示或暗示保证。 
   
3. 作者对因使用或无法使用本存储库及其附带文档所导致的任何损失，包括但不限于直接、间接、附带、继发或特殊损失，即使已被事先告知此类损失的可能性，也概不负责。

4. 使用本存储库中的内容即表示您确认并承担其使用的所有风险。此外，您同意作者不对因此使用而引发的任何问题或后果承担责任。

</details> 

---

<h3 align="center"> 
💖 支持此项目 
</h3>

<p align="center"> 
如果该项目对您有帮助，可以通过点赞支持他: :star2: 
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
即使是微薄的捐赠，我们也深表感谢。😌 
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
  <sub> 感谢您关注并支持这个项目 💙 </sub>
</p>

</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 

<h2 align="center">
  <a href="#-前言">📖 Unreal Engine 5.7 缓存配置指南</a>
</h2>

## 📚 目录

- [🎯 前言 - 为什么需要这个](#-前言)
- [💾 什么是缓存及其存储内容](#1-缓存中存储的内容是什么)
- [🏗️ 缓存系统的构造](#2-ue-57中的ddc架构)
- [📁 通过共享文件夹的简单配置](#3-通过-filesystem进行共享ddc配置)
- [☁️ 基于MinIO的高级配置（推荐）](#4-通过-minio配置共享ddc推荐方式)
- [🤖 自动化与CI/CD](#5-ddc与cicd至关重要)
- [📦 项目其他文件的存储](#6-content-storage不仅仅是ddc)
- [🔢 缓存版本-重要规则](#7-缓存版本常见错误)
- [📊 监控及清理旧文件](#8-监控与清理)
- [⚠️ 什么不应该缓存](#9-什么不需要缓存)
- [📈 实际提速数据](#10-效果数据)
- [🛠️ 最低工具配置](#11-推荐的最低技术栈)
- [📚 补充材料](#补充)

---

## 🔗 实用链接

**官方文档:**
- [Unreal Engine 文档](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**工具:**
- [MinIO 官方网站](https://min.io/)
- [Docker 文档](https://docs.docker.com/)

**社区:**
- [Unreal Engine 论坛](https://forums.unrealengine.com/)
- [Unreal Slackers Discord](https://unrealslackers.org/)

---

<h2 align="center">
  <a href="#-目录">⬆️ 返回目录</a>
</h2>


<h2 align="center">
  <a href="#-目录">⬆️ 顶部</a>
</h2>
```
