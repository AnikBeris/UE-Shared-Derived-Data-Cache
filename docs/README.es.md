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
    <img alt="Project Logo" src="../media/logo-light.png" width="512" height="auto">
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
Unreal Engine 5.7 – Shared Derived Data Cache (DDC) y Almacenamiento de Contenido
</h1>

<h2 align="center">
> 💡 Este documento describe la arquitectura, el propósito y la implementación práctica de **Shared Derived Data Cache (DDC)** y **Content Storage** para Unreal Engine 5.7 utilizando Docker y MinIO. El material está dirigido a desarrolladores individuales y pequeños equipos, y también es escalable para CI/CD.

</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
⚠️ Descargo de responsabilidad ⚠️
</h2>

<p align="center">
  El autor no se hace responsable de las posibles consecuencias del uso de este proyecto.<br>
  Úselo bajo su propio riesgo.
</p>

<details align="center"> 
    <summary>⚠️texto completo⚠️</summary>
    
Utilice los materiales de este repositorio bajo su propio riesgo.

1. Al utilizar este repositorio, usted acepta automáticamente los términos del acuerdo de licencia relacionado con él.

2. El autor no ofrece ninguna garantía, ya sea explícita o implícita, sobre la precisión, integridad o idoneidad de estos materiales para cualquier propósito específico. 
   
3. El autor no es responsable de ninguna pérdida, incluyendo pero no limitándose a pérdidas directas, indirectas, incidentales, o especiales, resultantes del uso o incapacidad de uso de materiales de este repositorio o de la documentación que lo acompaña, incluso si se informó previamente de la posibilidad de tales daños.

4. Al utilizar este repositorio, confirma y acepta todos los riesgos relacionados con su uso. Además, acepta que el autor no puede ser considerado responsable de ningún problema o consecuencia derivada de su uso.

</details> 

---

<h3 align="center"> 
💖 Apoya el proyecto 
</h3>

<p align="center"> 
Si este proyecto te ha sido útil, puedes mostrar tu apoyo dejando una estrella.:star2: 
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
Las donaciones son bienvenidas, no importa lo pequeñas que sean, y muchas gracias. 😌 
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
  <sub> Gracias por tu interés en el proyecto y por tu apoyo 💙 </sub>
</p>


</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
  <a href="#-contenido">📖 Guía para configurar el caché de Unreal Engine 5.7</a>
</h2>

## 📚 Contenido

- [🎯 Introducción - Por qué es necesario](#-introducción)
- [💾 Qué es el caché y qué se almacena en él](#1-qué-se-cachea-en-unreal-engine)
- [🏗️ Estructura del sistema de caché](#2-arquitectura-del-ddc-en-ue-57)
- [📁 Configuración sencilla desde una carpeta compartida](#3-shared-ddc-mediante-filesystem)
- [☁️ Configuración avanzada mediante MinIO (la mejor opción)](#4-shared-ddc-mediante-minio-opción-recomendada)
- [🤖 Automatización y CI/CD](#5-ddc-y-cicd-crucial)
- [📦 Almacenamiento de otros archivos del proyecto](#6-content-storage-no-solo-ddc)
- [🔢 Versiones del caché - reglas importantes](#7-versionado-del-caché-error-común)
- [📊 Monitoreo y limpieza de archivos antiguos](#8-monitoreo-y-limpieza)
- [⚠️ Qué NO deberías cachear](#9-qué-no-deberías-cachear)
- [📈 Resultado en cifras](#10-resultado-en-cifras)
- [🛠️ Conjunto mínimo de herramientas](#11-conjunto-mínimo-recomendado)
- [📚 Materiales adicionales](#adicionales)

---

## 🔗 Enlaces útiles

**Documentación oficial:**
- [Unreal Engine Documentation](https://docs.unrealengine.com/)
- [Derived Data Cache](https://docs.unrealengine.com/5.7/derived-data-cache/)

**Herramientas:**
- [MinIO Official Site](https://min.io/)
- [Docker Documentation](https://docs.docker.com/)

**Comunidades:**
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

## 4️⃣ Shared DDC mediante MinIO (opción recomendada)

### 4.1 ¿Qué es MinIO y para qué sirve?

**En términos simples** `MinIO` - Es un programa que convierte una carpeta común en almacenamiento en la nube (como AWS S3, pero propio).

<details> 
    <summary>⚙️ Desplegar descripción</summary>

**Analogía:**
- **Filesystem** = una carpeta normal en el ordenador
- **MinIO** = la misma carpeta, pero con interfaz web y acceso remoto por internet

**¿Por qué MinIO es mejor que una carpeta común?**

| Función | Carpeta común | MinIO |
|---------|---------------|-------|
| Operar en red local | ✅ Sí | ✅ Sí |
| Operar por internet | ❌ Difícil | ✅ Fácil |
| Versionado de archivos | ❌ No | ✅ Sí |
| Limpieza automática de archivos antiguos | ❌ Manual | ✅ Automático |
| Monitoreo de uso | ❌ No | ✅ Sí |
| Escalabilidad | ❌ Limitada | ✅ Excelente |
| Seguridad | ⚠️ Básica | ✅ Avanzada |

</details>

---

### 4.2 Arquitectura de funcionamiento

<details> 
    <summary>⚙️ Desplegar descripción</summary>

```
Desarrollador (Unreal Editor)
         ↓ Solicitud HTTP
      Servidor MinIO
         ↓
    Carpeta en el disco
    /minio-data/
      ├─ ue-ddc-5-7/     ← Bucket (contenedor para archivos)
      │   ├─ Win64/
      │   └─ Linux/
```

**¿Qué es un Bucket?**
- `Bucket` = carpeta virtual dentro de MinIO. Es como un "cajón" separado para almacenaje.

</details>


### 4.3 Instalación de MinIO mediante Docker

<details> 
    <summary>⚙️ Desplegar descripción</summary>

#### ¿Por qué Docker?

- `Docker` = forma de ejecutar una aplicación en un "contenedor" que no depende de tu sistema operativo.

**Ventajas:**
- ✅ Funciona igual en Windows, Linux, Mac
- ✅ No requiere instalación de dependencias
- ✅ Fácil de eliminar si no te interesa
- ✅ Aislado del resto del sistema

</details>

---


<h2 align="center">
  <a href="#-contenido">⬆️ Volver al contenido</a>
</h2>

---


