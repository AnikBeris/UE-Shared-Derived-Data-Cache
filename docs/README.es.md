<p align="center">
  <strong>-------></strong>
  <a href="/README.md">Ruso</a> |
  <a href="/docs/README.en.md">Inglés</a> |
  <a href="/docs/README.es.md">Español</a> |
  <a href="/docs/README.zh.md">Chino</a> |
  <strong><-------</strong>
</p>


<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="../media/logo-dark.png">
    <img alt="Logotipo del proyecto" src="../media/logo-light.png" width="512" height="auto">
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
> 💡 El material está orientado a desarrolladores indie y pequeños equipos, además es escalable para entornos CI/CD y estudios medianos.
> 💡 Este documento describe la arquitectura, el propósito y la implementación práctica de Shared Derived Data Cache (DDC) y Content Storage para Unreal Engine 5.7 utilizando Docker y MinIO.

</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
⚠️ Exención de responsabilidad ⚠️
</h2>

<p align="center">
  El autor no se responsabiliza por las posibles consecuencias del uso de este proyecto.<br>
  Utilícelo bajo su propio riesgo.
</p>

<details align="center"> 
    <summary>⚠️texto completo⚠️</summary>
    
Utilice los materiales de este repositorio bajo su propio riesgo.

1. Al utilizar los materiales de este repositorio, acepta automáticamente los términos del acuerdo de licencia asociado.

2. El autor no ofrece ninguna garantía, ya sea explícita o implícita, respecto a la precisión, integridad o idoneidad de estos materiales para cualquier propósito en particular.

3. El autor no se hace responsable por ninguna pérdida, incluidos pero no limitados a pérdidas directas, indirectas, incidentales, consecuentes o especiales que surjan del uso o la incapacidad de usar los materiales de este repositorio o su documentación asociada, incluso si se advirtió previamente sobre la posibilidad de tales daños.

4. Al utilizar estos materiales de este repositorio, usted confirma y asume todos los riesgos asociados con su aplicación. Además, acepta que el autor no puede ser considerado responsable de cualquier problema o consecuencia derivada de su uso.

</details> 

---

<h3 align="center"> 
💖 Apoye el proyecto 
</h3>

<p align="center"> 
Si este proyecto le ha sido útil, puede apoyarlo dejando una estrella.:star2: 
</p>


<details align="center"> 
    <summary>💖Apoye el proyecto💖</summary>

<p align="center">
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="Buy Me a Coffee">
  </a>
  <a href="https://pay.cloudtips.ru/p/7249ba98" target="_blank">
    <img src="../media/buymeacoffe.png" alt="Buy Me a Coffee">
  </a>
</p>



<h4 align="center"> 
Las donaciones son bienvenidas, sin importar lo pequeñas que sean. Muchas gracias. 😌 
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
  <sub> Gracias por su interés en el proyecto y su apoyo 💙 </sub>
</p>


</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
  <a href="#-contenido">📖 Guía de configuración del caché de Unreal Engine 5.7</a>
</h2>

## 📚 Contenido

- [🎯 Introducción - Por qué es necesario](#-introducción)
- [💾 Qué es un caché y qué contiene](#1-qué-se-cachéa-en-unreal-engine)
- [🏗️ Cómo está configurado el sistema de caché](#2-arquitectura-del-ddc-en-ue-57)
- [📁 Configuración sencilla a través de una carpeta compartida](#3-shared-ddc-a-través-de-filesystem)
- [☁️ Configuración avanzada con MinIO (mejor opción)](#4-shared-ddc-a-través-de-minio-opción-recomendada)
- [🤖 Automatización y CI/CD](#5-ddc-y-cicd-críticamente-importante)
- [📦 Almacenamiento de otros archivos del proyecto](#6-content-storage-no-solo-ddc)
- [🔢 Versiones del caché - reglas importantes](#7-versionado-del-caché-error-común)
- [📊 Supervisión y limpieza de archivos antiguos](#8-supervisión-y-limpieza)
- [⚠️ Qué NO debe cachéarse](#9-qué-no-es-necesario-cachéar)
- [📈 Cifras reales de aceleración](#10-resultado-en-cifras)
- [🛠️ Conjunto mínimo de herramientas](#11-conjunto-mínimo-recomendado)
- [📚 Materiales adicionales](#adicionalmente)

---

## 🔗 Enlaces útiles

**Documentación oficial:**
- [Documentación de Unreal Engine](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**Herramientas:**
- [MinIO Página Oficial](https://min.io/)
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

## 4️⃣ Shared DDC a través de MinIO (opción recomendada)

### 4.1 ¿Qué es MinIO y por qué es necesario?

**En palabras simples** `MinIO` es un programa que convierte una carpeta ordinaria en un almacenamiento en la nube (como AWS S3, pero propio).

<details> 
    <summary>⚙️ Desplegar descripción</summary>

**Analogía:**
- **Filesystem** = una carpeta normal en tu computadora
- **MinIO** = esa misma carpeta, pero con interfaz web y acceso desde internet

**¿Por qué MinIO es mejor que una carpeta normal?**

| Función | Carpeta normal | MinIO |
|---------|----------------|-------|
| Funciona en red local | ✅ Sí | ✅ Sí |
| Funciona a través de internet | ❌ Complicado | ✅ Fácil |
| Versionado de archivos | ❌ No | ✅ Sí |
| Limpieza automática de archivos antiguos | ❌ Manual | ✅ Automático |
| Supervisión del uso | ❌ No | ✅ Sí |
| Escalabilidad | ❌ Limitado | ✅ Excelente |
| Seguridad | ⚠️ Básica | ✅ Avanzada |

</details> 


