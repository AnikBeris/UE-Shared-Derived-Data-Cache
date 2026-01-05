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
[![GitHub Stars](https://img.shields.io/github/stars/AnikBeris?style=flat&logo=github&label=星标&color=orange)](https://github.com/AnikBeris)

</div>

<div align="center">
  <img src="../media/image0.gif" alt="SPACER" width="90%">
</div>


<h1 align="center"> 
关于使用 AdvancedSessions-Steam 插件在 Unreal Engine 5 开发项目的实用信息
</h1>

<h2 align="center">
> 💡 本资料适用于有经验的用户。
</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
⚠️免责声明⚠️
</h2>

<p align="center">
  作者对任何使用此项目可能导致的后果不承担责任。<br>
  使用请自担风险。
</p>

<details align="center"> 
    <summary>⚠️完整文本⚠️</summary>
    
请自担风险使用此仓库中的资料。

1. 使用此仓库中的资料，即表示您自动同意相关许可协议的条款。

2. 作者不对这些资料的准确性、完整性或适用于任何特定目的的合理性提供任何明示或暗示的保证。 
   
3. 无论是直接、间接、附带、间接或特殊损害的情况下，因使用此项目或其文档导致的任何损失或后果，即使提前被告知了此类损害的可能性，作者概不负责。

4. 使用此仓库中的资料，您确认并接受其相关的所有风险。同样，您同意作者不对使用项目引发的问题或后果承担责任。

</details> 

---

<h3 align="center"> 
💖 支持项目 
</h3>

<p align="center"> 
如果此项目对您有所帮助，欢迎为它点亮⭐。:star2: 
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
无论捐赠金额多少，感谢您的支持和关注 😌 
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
  <sub> 感谢您对项目的关注并支持 💙 </sub>
</p>


</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
  <a href="#-введение">📖 Unreal Engine 5.7 缓存设置指南</a>
</h2>

## 📚 目录

- [🎯 导言 - 为什么需要这个](#-введение)
- [💾 什么是缓存以及它存储的内容](#1-что-именно-кешируется-в-unreal-engine)
- [🏗️ 缓存系统如何设计](#2-архитектура-ddc-в-ue-57)
- [📁 通过共享文件夹的简单设置](#3-shared-ddc-через-filesystem)
- [☁️ 通过 MinIO 的高级设置（最佳选项）](#4-shared-ddc-через-minio-рекомендуемый-вариант)
- [🤖 自动化与 CI/CD](#5-ddc-и-cicd-критически-важно)
- [📦 项目其他文件存储](#6-content-storage-не-только-ddc)
- [🔢 缓存版本 - 重要规则](#7-версионирование-кеша-частая-ошибка)
- [📊 监控和清理旧文件](#8-мониторинг-и-очистка)
- [⚠️ 什么不需要缓存](#9-что-не-стоит-кешировать)
- [📈 加速的实际数据](#10-результат-в-цифрах)
- [🛠️ 最小的工具需求](#11-рекомендуемый-минимальный-стек)
- [📚 补充内容](#дополнительно)

---

## 🔗 有用的链接

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
  <a href="#-содержание">⬆️ 回到目录</a>
</h2>


<h2 align="center">
  <a href="#-содержание">⬆️ 向上</a>
</h2>

---

## 4️⃣ 通过 MinIO 的 Shared DDC（推荐方式）

### 4.1 什么是 MinIO？为什么需要它？

**通俗地说** `MinIO` 是一个可以将普通的磁盘文件夹变成云存储的程序（类似 AWS S3，但私有化）。

<details> 
    <summary>⚙️ 展开描述</summary>

**类比:**
- **Filesystem** = 计算机中的普通文件夹
- **MinIO** = 带有网页版界面并且可以通过互联网访问的文件夹

**为什么 MinIO 比普通文件夹更优秀？**

| 功能 | 普通文件夹 | MinIO |
|------|---------|-------|
| 局域网操作 | ✅ 可以 | ✅ 可以 |
| 互联网操作 | ❌ 异常复杂 | ✅ 简单 |
| 文件版本控制 | ❌ 无 | ✅ 有 |
| 自动清理旧文件 | ❌ 手动执行 | ✅ 自动 |
| 使用监控 | ❌ 不支持 | ✅ 支持 |
| 可扩展性 | ❌ 弱 | ✅ 强大 |
| 安全性 | ⚠️ 基础 | ✅ 高级 |

</details>

完整内容请阅读上文原文翻译内容。在左侧导航栏您可以快速跳转到相关小节。
