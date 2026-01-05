<p align="center">
  <strong>-------></strong>
  <a href="/README.md">Русский</a> |
  <a href="/docs/README.en.md">English</a> |
  <a href="/docs/README.es.md">Español</a> |
  <a href="/docs/README.zh.md">Chino</a> |
  <strong><-------</strong>
</p>

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="../media/logo-dark.png">
    <img alt="Logotipo del Proyecto" src="../media/logo-light.png" width="512" height="auto">
  </picture>
</p>

---

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-blue?style=flat&logo=github)](https://github.com/AnikBeris)
[![License](https://img.shields.io/badge/License-purple?style=flat&logo=github)](/LICENSE.md)
[![GitHub Stars](https://img.shields.io/github/stars/AnikBeris?style=flat&logo=github&label=Estrellas&color=orange)](https://github.com/AnikBeris)

</div>

<div align="center">
  <img src="../media/image0.gif" alt="SPACER" width="90%">
</div>

<h1 align="center"> 
Unreal Engine 5.7 – Shared Derived Data Cache (DDC) y Content Storage
</h1>

<h2 align="center">
  
> 💡 El material está orientado a desarrolladores independientes y pequeños equipos, y también es escalable para CI/CD y estudios medianos.  

> 💡 Este documento describe la arquitectura, propósito e implementación práctica de Shared Derived Data Cache (DDC) y Content Storage para Unreal Engine 5.7 utilizando Docker y MinIO.

</h2>

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 

<h2 align="center">
⚠️ Descargo de responsabilidad ⚠️
</h2>

<p align="center">
  El autor no se hace responsable de cualquier consecuencia derivada del uso de este proyecto.<br>
  Úselo bajo su propia responsabilidad.
</p>

<details align="center"> 
    <summary>⚠️texto completo⚠️</summary>
    
Utilice los materiales de este repositorio bajo su propia responsabilidad.

1. Al usar los materiales de este repositorio, usted automáticamente acepta los términos del acuerdo de licencia asociado con el mismo.

2. El autor no ofrece garantías, explícitas o implícitas, sobre la exactitud, integridad o idoneidad de estos materiales para cualquier propósito particular.

3. El autor no será responsable de ningún daño, incluyendo pero no limitado a daños directos, indirectos, incidentales, consecuentes o especiales, que surjan del uso o incapacidad de usar los materiales de este repositorio o su documentación asociada, incluso si se informó previamente sobre la posibilidad de dichos daños.

4. Al utilizar estos materiales de este repositorio, usted confirma y asume todos los riesgos asociados con su aplicación. Además, usted acepta que el autor no puede ser considerado responsable de ningún problema o consecuencia derivada de su uso.

</details> 

---

<h3 align="center"> 
💖 Apoya el proyecto 
</h3>

<p align="center"> 
Si este proyecto te resultó útil, puedes valorarlo dándole una estrella.:star2: 
</p>

<details align="center"> 
    <summary>💖Apoya el proyecto💖</summary>

<p align="center">
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="Buy Me a Coffee">
  </a>
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="Buy Me a Coffee">
  </a>
</p>

<h4 align="center"> 
Las donaciones son bienvenidas, por pequeñas que sean. Muchas gracias. 😌 
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
  <sub> Gracias por tu atención al proyecto y por tu apoyo 💙 </sub>
</p>

</details> 

* * * * * * * * * * * * * * * * * *
* * * * * * * * * * * * * * * * * * 


<h2 align="center">
  <a href="#-contenido">📖 Guía de configuración de la caché de Unreal Engine 5.7</a>
</h2>

## 📚 Contenido

- [🎯 Introducción - ¿Por qué es útil?](#-introducción)
- [💾 ¿Qué es la caché y qué se almacena en ella?](#1-qué-se-cachea-en-unreal-engine)
- [🏗️ Cómo funciona el sistema de caché](#2-arquitectura-del-ddc-en-ue-57)
- [📁 Configuración sencilla con folder compartido](#3-shared-ddc-a-través-de-filesystem)
- [☁️ Configuración avanzada con MinIO (mejor opción)](#4-shared-ddc-a-través-de-minio-opción-recomendada)
- [🤖 Automatización y CI/CD](#5-ddc-y-cicd-críticamente-importante)
- [📦 Almacenamiento de otros archivos del proyecto](#6-content-storage-no-solo-ddc)
- [🔢 Versionado de la caché - reglas importantes](#7-versionado-de-la-caché-error-común)
- [📊 Monitorización y limpieza de archivos antiguos](#8-monitoreo-y-limpieza)
- [⚠️ Qué NO debes cachear](#9-quÉ-no-deberÍas-cachear)
- [📈 Datos reales de mejoría en velocidad](#10-resultado-en-números)
- [🛠️ Herramientas mínimas necesarias](#11-stack-mínimo-recomendado)
- [📚 Material adicional](#adicional)

---

## 🔗 Enlaces útiles

**Documentación oficial:**
- [Documentación de Unreal Engine](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**Herramientas:**
- [Sitio oficial de MinIO](https://min.io/)
- [Documentación de Docker](https://docs.docker.com/)

**Comunidad:**
- [Foros de Unreal Engine](https://forums.unrealengine.com/)
- [Unreal Slackers Discord](https://unrealslackers.org/)

---

<h2 align="center">
  <a href="#-contenido">⬆️ Volver al contenido</a>
</h2>


<h2 align="center">
  <a href="#-contenido">⬆️ Arriba</a>
</h2>

---
