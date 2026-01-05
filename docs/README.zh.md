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
[![GitHub Stars](https://img.shields.io/github/stars/AnikBeris?style=flat&logo=github&label=Звёзды&color=orange)](https://github.com/AnikBeris)

</div>

<div align="center">
  <img src="../media/image0.gif" alt="SPACER" width="90%">
</div>


<h1 align="center"> 
Unreal Engine 5.7 – Shared Derived Data Cache (DDC) 和 Content Storage
</h1>

<h2 align="center">
  
> 💡 本文档面向独立开发人员与小型团队，也适用于CI/CD和中型工作室。

> 💡 本文档描述了使用 Docker 和 MinIO 实现 Unreal Engine 5.7 的 Shared Derived Data Cache (DDC) 和 Content Storage 的架构、用途及实践。

</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 




<h2 align="center">
⚠️ 注意事项 ⚠️
</h2>

<p align="center">
  作者不对使用本项目可能造成的任何后果承担责任。<br>
  风险自负。
</p>

<details align="center"> 
    <summary>⚠️完整声明⚠️</summary>
    
使用本存储库的资料需自行承担风险。

1. 使用本存储库的材料即表示您同意与之相关的许可协议的条款。

2. 作者对这些材料的准确性、完整性或适用于特定用途的适用性不作任何明示或暗示的保证。

3. 作者对因使用或无法使用本存储库或其相关文档中的材料而造成的任何损失，包括但不限于直接损失、间接损失、附带损失或特殊损失即使已经提前被告知也不负责。

4. 使用本存储库的材料即表示您确认并承担所有相关的风险。同时，您同意作者在此项目使用中产生的任何问题或后果中，不对其承担责任。

</details> 

---

<h3 align="center"> 
💖 支持此项目 
</h3>

<p align="center"> 
如果此项目对您有帮助，您可以通过点亮星星来支持它。:star2: 
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
不管金额大小，都非常欢迎任何捐助，感谢支持。😌 
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
  <a href="#-содержание">📖 Unreal Engine 5.7 缓存设置指南</a>
</h2>

## 📚 内容目录

- [🎯 引言 - 为什么需要它](#-введение)
- [💾 缓存是什么以及它存储了什么](#1-что-именно-кешируется-в-unreal-engine)
- [🏗️ 缓存系统的构造](#2-архитектура-ddc-в-ue-57)
- [📁 使用共享文件夹的简单设置](#3-shared-ddc-через-filesystem)
- [☁️ 通过 MinIO 的高级设置（推荐方式）](#4-shared-ddc-через-minio-рекомендуемый-вариант)
- [🤖 自动化和CI/CD](#5-ddc-и-cicd-критически-важно)
- [📦 项目的其他文件存储](#6-content-storage-не-только-ddc)
- [🔢 缓存版本 - 重要规则](#7-версионирование-кеша-частая-ошибка)
- [📊 文件监控与清理](#8-мониторинг-и-очистка)
- [⚠️ 什么不应该缓存](#9-что-не-стоит-кешировать)
- [📈 实际加速数据](#10-результат-в-цифрах)
- [🛠️ 工具所需最低配置](#11-рекомендуемый-минимальный-стек)
- [📚 更多资料](#дополнительно)

---

## 🔗 相关链接

**官方文档：**
- [Unreal Engine Documentation](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**工具：**
- [MinIO 官方网站](https://min.io/)
- [Docker 文档](https://docs.docker.com/)

**社区：**
- [Unreal Engine Forums](https://forums.unrealengine.com/)
- [Unreal Slackers Discord](https://unrealslackers.org/)

---

<h2 align="center">
  <a href="#-содержание">⬆️ 返回目录</a>
</h2>


<h2 align="center">
  <a href="#-содержание">⬆️ 返回顶部</a>
</h2>

---

## 4️⃣ 通过 MinIO 的 Shared DDC（推荐方式）

### 4.1 什么是 MinIO？为什么需要它？

**简单来说** `MinIO` 是一个程序，可以将磁盘上的普通文件夹变成云存储（类似 AWS S3，但属于自己管理）。

<details> 
    <summary>⚙️ 展开描述</summary>

**类比：**
- **Filesystem** = 计算机上的普通文件夹
- **MinIO** = 同一个文件夹，但有了网页界面并支持互联网访问

**相比普通文件夹，MinIO 的优势：**

| 功能 | 普通文件夹 | MinIO |
|---------|---------------|-------|
| 局域网工作 | ✅ 是 | ✅ 是 |
| 互联网访问 | ❌ 困难 | ✅ 简单 |
| 文件版本控制 | ❌ 没有 | ✅ 有 |
| 自动清理旧文件 | ❌ 手动 | ✅ 自动 |
| 使用情况监测 | ❌ 无 | ✅ 支持 |
| 可扩展性 | ❌ 一般 | ✅ 优秀 |
| 安全性 | ⚠️ 基础 | ✅ 优化 |

</details>

---

### 4.2 工作架构

<details> 
    <summary>⚙️ 展开描述</summary>

```
开发者 (Unreal Editor)
         ↓ HTTP 请求
      MinIO 服务器
         ↓
    硬盘上的文件夹
    /minio-data/
      ├─ ue-ddc-5-7/     ← Bucket（文件容器）
      │   ├─ Win64/
      │   └─ Linux/
```

**什么是 Bucket？**
- `Bucket` = MinIO 中的虚拟文件夹。相当于用于存储文件的单独“箱子”。

</details>


### 4.3 通过 Docker 安装 MinIO

<details> 
    <summary>⚙️ 展开描述</summary>

#### 为什么使用 Docker？

- `Docker` = 可在独立环境中运行的“容器”，不依赖于您的系统。

**优点：**
- ✅ 在 Windows、Linux、Mac 上都能正常运行
- ✅ 无需安装额外依赖项
- ✅ 不喜欢时可轻松删除
- ✅ 与操作系统隔离

</details>

---


<h2 align="center">
  <a href="#-содержание">⬆️ 返回目录</a>
</h2>

---

---

<h1 align="center"> 📜 许可证 </h1>
<p align="center">
  <strong> 本项目基于 </strong> 
  <a href="/LICENSE">Apache License</a> 
  <strong> 发布 </strong>
</p>

---

<h2 align="center"> 
详细文档请查看 
</h2>


<p align="center">
  <strong>-------></strong>
  <a href="/README.md">Русский</a> |
  <a href="/docs/README.en.md">English</a> |
  <a href="/docs/README.es.md">Spanish</a> |
  <a href="/docs/README.zh.md">Chinese</a> |
  <strong><-------</strong>
</p>
