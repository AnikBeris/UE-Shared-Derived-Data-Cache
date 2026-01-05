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
  <img src="../media/image0.gif" alt="ESPACIADOR" width="90%">
</div>


<h1 align="center"> 
Unreal Engine 5.7 – Shared Derived Data Cache (DDC) y Almacenamiento de Contenido
</h1>

<h2 align="center">
> 💡 Este documento describe la arquitectura, propósito y la implementación práctica de **Shared Derived Data Cache (DDC)** y **Content Storage** para Unreal Engine 5.7 utilizando Docker y MinIO. El material está destinado a desarrolladores individuales y pequeños equipos, además de ser escalable para CI/CD.

</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
⚠️ Descargo de responsabilidad ⚠️
</h2>

<p align="center">
  El autor no se responsabiliza por las posibles consecuencias del uso de este proyecto.<br>
  Use bajo su propio riesgo.
</p>

<details align="center"> 
    <summary>⚠️texto completo⚠️</summary>
    
Use los materiales de este repositorio bajo su propio riesgo.

1. Al utilizar los materiales de este repositorio, automáticamente acepta los términos del acuerdo de licencia relacionado con él.

2. El autor no otorga ninguna garantía, expresa o implícita, sobre la precisión, integridad o idoneidad de estos materiales para ningún propósito en particular. 
   
3. El autor no es responsable por cualquier daño, incluyendo, pero no limitado a, daños directos, indirectos, incidentales, consecuenciales o especiales, que puedan surgir del uso o incapacidad de uso de los materiales de este repositorio o su documentación acompañante, incluso si se ha advertido previamente de la posibilidad de tales daños.

4. Al usar los materiales de este repositorio, usted acepta y asume todos los riesgos relacionados con su aplicación. Además, acepta que el autor no puede ser responsabilizado por ningún problema o consecuencia derivada de su uso.

</details> 

---

<h3 align="center"> 
💖 Apoye el proyecto 
</h3>

<p align="center"> 
Si este proyecto le ha resultado útil, puede valorarlo dejando una estrella.:star2: 
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
Las donaciones son muy bienvenidas, no importa cuán pequeñas sean, y muchas gracias. 😌 
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
  <sub> Gracias por su atención al proyecto y por su apoyo 💙 </sub>
</p>


</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
  <a href="#-introducción">📖 Guía de configuración del caché de Unreal Engine 5.7</a>
</h2>

## 📚 Contenido

- [🎯 Introducción - ¿Por qué es necesario?](#-introducción)
- [💾 ¿Qué es el caché y qué se almacena en él?](#1-qué-se-cachea-en-unreal-engine)
- [🏗️ ¿Cómo funciona el sistema de caché?](#2-arquitectura-ddc-en-ue-57)
- [📁 Configuración simple a través de una carpeta compartida](#3-shared-ddc-a-través-de-filesystem)
- [☁️ Configuración avanzada mediante MinIO (mejor opción)](#4-shared-ddc-a-través-de-minio-opción-recomendada)
- [🤖 Automatización y CI/CD](#5-ddc-y-cicd-críticamente-importante)
- [📦 Almacenamiento de otros archivos del proyecto](#6-content-storage-no-solo-ddc)
- [🔢 Versiones de caché - Reglas importantes](#7-versionado-del-caché-error-común)
- [📊 Monitoreo y limpieza de archivos antiguos](#8-monitoreo-y-limpieza)
- [⚠️ Qué NO se debe cachear](#9-lo-que-no-debería-cachearse)
- [📈 Números reales de aceleración](#10-resultados-en-números)
- [🛠️ Conjunto mínimo de herramientas](#11-stack-mínimo-recomendado)
- [📚 Materiales adicionales](#adicional)

---

## 🔗 Enlaces útiles

**Documentación oficial:**
- [Unreal Engine Documentation](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**Herramientas:**
- [MinIO Official Site](https://min.io/)
- [Docker Documentation](https://docs.docker.com/)

**Comunidad:**
- [Unreal Engine Forums](https://forums.unrealengine.com/)
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

### 4.1 ¿Qué es MinIO y por qué lo necesitas?

**en palabras simples** `MinIO` - Es un programa que convierte una carpeta normal en un disco en un almacenamiento en la nube (como AWS S3, pero propio).

<details> 
    <summary>⚙️ Desplegar descripción</summary>

**Analogía:**
- **Filesystem** = una carpeta normal en tu computadora
- **MinIO** = la misma carpeta, pero con interfaz web y acceso a través de internet

**¿Por qué MinIO es mejor que una carpeta normal?**

| Funcionalidad | Carpeta normal | MinIO |
|---------|---------------|-------|
| Funciona en red local | ✅ Sí | ✅ Sí |
| Funciona a través de internet | ❌ Difícil | ✅ Fácil |
| Versionado de archivos | ❌ No | ✅ Sí |
| Limpieza automática de archivos antiguos | ❌ Manual | ✅ Automáticamente |
| Monitoreo del uso | ❌ No | ✅ Sí |
| Escalabilidad | ❌ Limitada | ✅ Alta |
| Seguridad | ⚠️ Básica | ✅ Avanzada |

</details>

---

### 4.2 Arquitectura de funcionamiento

<details> 
    <summary>⚙️ Desplegar descripción</summary>

```
Desarrollador (Unreal Editor)
         ↓ HTTP request
      Servidor MinIO
         ↓
    Carpeta en el disco
    /minio-data/
      ├─ ue-ddc-5-7/     ← Bucket (contenedor para archivos)
      │   ├─ Win64/
      │   └─ Linux/
```

**¿Qué es un Bucket?**
- `Bucket` = una carpeta virtual dentro de MinIO. Es como un "cajón" separado para almacenar archivos.

</details>
