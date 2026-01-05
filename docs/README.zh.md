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
    <img alt="项目Logo" src="../media/logo-light.png" width="512" height="auto">
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
虚幻引擎5.7 - 共享派生数据缓存 (DDC) 和内容存储
</h1>

<h2 align="center">
> 💡 本文档面向独立开发者和小团队，同时也适用于 CI/CD 和中型工作室的扩展需求。
> 💡 本文档描述了使用 Docker 和 MinIO 的虚幻引擎 5.7 的共享派生数据缓存 (DDC) 和内容存储的架构、用途和实践实现。

</h2>

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 

<h2 align="center">
⚠️免责声明⚠️
</h2>

<p align="center">
  作者不对任何因使用本项目可能引发的后果负责。<br>
  一切风险由使用者承担。
</p>

<details align="center"> 
    <summary>⚠️完整文本⚠️</summary>
    
使用本仓库的材料需默认接受以下条款：

1. 使用本仓库中的材料，即表示您自动同意与之相关的许可协议中的条款。

2. 作者不对材料的准确性、完整性或适用于某特定目的的适用性提供任何明示或暗示的担保。

3. 作者不对因使用或无法使用本仓库中的材料或其附带文档而造成的任何损失负责，包括但不限于直接损失、间接损失、附带损失、后果性损失或特殊损失，即便事先已告知可能性。

4. 使用本仓库的材料即表示您确认并承担所有与其使用相关风险。此外，您同意作者不对由其使用引起的任何问题或后果负责。

</details> 

---

<h3 align="center"> 
💖 支持项目 
</h3>

<p align="center"> 
如果您觉得此项目对您有用，欢迎给项目点个赞:star2: 
</p>

<details align="center"> 
    <summary>💖支持项目💖</summary>

<p align="center">
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="请我喝杯咖啡">
  </a>
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="请我喝杯咖啡">
  </a>
</p>

<h4 align="center"> 
非常感谢您的支持，无论捐赠金额多么微小 😌 
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
  <sub> 感谢您对项目的关注与支持 💙 </sub>
</p>

</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 

<h2 align="center">
  <a href="#-目录">📖 虚幻引擎 5.7 缓存设置指南</a>
</h2>

## 📚 目录

- [🎯 引言 - 为什么需要](#-引言)
- [💾 缓存是什么以及它保存了什么](#1-虚幻引擎缓存了什么内容)
- [🏗️ 缓存系统的架构](#2-虚幻引擎-57-的ddc架构)
- [📁 通过共享文件夹实现简单配置](#3-通过文件系统实现共享ddc)
- [☁️ 通过MinIO的高级设置（推荐）](#4-通过minio实现共享ddc-推荐方案)
- [🤖 自动化与CI/CD](#5-ddc与cicd至关重要)
- [📦 项目文件的其他存储方式](#6-不仅仅是ddc的内容存储)
- [🔢 缓存版本的重要规则](#7-缓存版本化-常见错误)
- [📊 监控与清理旧文件](#8-监控与清理)
- [⚠️ 不需要缓存的内容](#9-不需要缓存的内容)
- [📈 实际加速数字](#10-加速效果数据)
- [🛠️ 最低工具要求](#11-推荐的最低工具集)
- [📚 补充资料](#补充材料)

---

## 🔗 实用链接

**官方文档：**
- [虚幻引擎文档](https://docs.unrealengine.com/)
- [派生数据缓存](https://docs.unrealengine.com/5.7/derived-data-cache/)

**工具：**
- [MinIO 官方网站](https://min.io/)
- [Docker 文档](https://docs.docker.com/)

**社区：**
- [虚幻引擎论坛](https://forums.unrealengine.com/)
- [Unreal Slackers Discord](https://unrealslackers.org/)

---

<h2 align="center">
  <a href="#-目录">⬆️ 返回目录</a>
</h2>

<h2 align="center">
  <a href="#-目录">⬆️ 顶部</a>
</h2>

---

## 4️⃣ 通过MinIO实现共享DDC（推荐方案）

### 4.1 MinIO 是什么，有什么用？

**简单来说** `MinIO` 是一款程序，能够将普通硬盘上的文件夹变成云存储（类似 AWS S3，但自托管）。

<details>
    <summary>⚙️展开说明</summary>

**类比：**
- **Filesystem** = 本地硬盘上的普通文件夹
- **MinIO** = 文件夹带有网络接口和Web界面支持

**为什么选择 MinIO？**

| 功能 | 普通文件夹 | MinIO |
|---------|---------------|-------|
| 局域网操作 | ✅ 是 | ✅ 是 |
| 互联网操作 | ❌ 难 | ✅ 简单 |
| 文件版本化 | ❌ 无 | ✅ 有 |
| 清理旧文件 | ❌ 手动 | ✅ 自动 |
| 使用监控 | ❌ 无 | ✅ 有 |
| 可扩展性 | ❌ 差 | ✅ 极好 |
| 安全性 | ⚠️ 基础 | ✅ 高级 |

</details>

--- 

### 4.2 系统架构

<details> 
    <summary>⚙️展开说明</summary>

```
开发人员 (虚幻编辑器)
         ↓ HTTP请求
      MinIO服务器
         ↓
    硬盘上的文件夹
    /minio-data/
      ├─ ue-ddc-5-7/     ← Bucket（文件存储容器）
      │   ├─ Win64/
      │   └─ Linux/
```

**什么是 Bucket？**
- `Bucket` 就是 MinIO 中的虚拟文件夹，类似独立的“箱子”来存储文件。

</details>

---

### 4.3 使用 Docker 安装 MinIO

<details> 
    <summary>⚙️展开说明</summary>

#### 为什么选择 Docker？

- `Docker` 是一种运行程序的方式，它类似“盒子”，不依赖您的系统环境。

**优势：**
- ✅ 在 Windows、Linux 和 Mac 上运行一致
- ✅ 无需安装依赖项
- ✅ 不喜欢时可以轻松删除
- ✅ 与其他系统独立隔离

</details>

--- 


<h2 align="center">
  <a href="#-目录">⬆️ 返回目录</a>
</h2>

---

---

# 第一步：安装 Docker

<details> 
    <summary>⚙️Windows </summary>

**Windows:**
1. 下载 [Docker Desktop](https://www.docker.com/products/docker-desktop)
2. 安装并运行
3. 确保 Docker 正常运行（任务栏托盘中出现鲸鱼图标）

</details>

<details> 
    <summary>⚙️Linux (Ubuntu/Debian) </summary>

```bash
# 安装 Docker
sudo apt update
sudo apt install docker.io docker-compose -y

# 将用户添加到 docker 用户组
sudo usermod -aG docker $USER

# 重新启动
sudo reboot
```

**安装验证：**
```bash
docker --version
```
# 应显示：`Docker version 24.0.x, build ...`

</details>

--- 

...
