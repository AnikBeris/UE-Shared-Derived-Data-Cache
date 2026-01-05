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
Unreal Engine 5.7 – Shared Derived Data Cache (DDC) y Content Storage
</h1>

<h2 align="center">
  
> 💡 El material está orientado a desarrolladores independientes y pequeños equipos, y también es adaptable para CI/CD y estudios medianos.

> 💡 Este documento describe la arquitectura, propósito e implementación práctica de Shared Derived Data Cache (DDC) y Content Storage para Unreal Engine 5.7 utilizando Docker y MinIO.

</h2>


* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 




<h2 align="center">
⚠️ Descargo de Responsabilidad ⚠️
</h2>

<p align="center">
  El autor no se responsabiliza por las posibles consecuencias de utilizar este proyecto.<br>
  Úselo bajo su propio riesgo.
</p>

<details align="center"> 
    <summary>⚠️texto completo⚠️</summary>
    
Utilice el material de este repositorio bajo su propio riesgo.

1. Al usar el contenido de este repositorio, acepta automáticamente los términos de la licencia asociada al mismo.

2. El autor no ofrece ninguna garantía, ya sea expresa o implícita, respecto a la exactitud, integridad o idoneidad de estos materiales para fines particulares. 
   
3. El autor no será responsable por ninguna pérdida, incluidos, entre otros, daños directos, indirectos, incidentales, consecuentes o especiales, derivados del uso o la incapacidad de usar el contenido de este repositorio o su documentación complementaria, incluso si se advirtió previamente sobre la posibilidad de tales daños.

4. Al utilizar este material, usted reconoce y asume todos los riesgos asociados con su implementación. Además, acepta que el autor no puede ser responsabilizado por ningún problema o consecuencia que surja como resultado de su uso.

</details> 

---

<h3 align="center"> 
💖 Apoye el proyecto 
</h3>

<p align="center"> 
Si este proyecto le ha resultado útil, puede valorarlo con una estrellita.:star2: 
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
Las donaciones son muy bienvenidas, por pequeñas que sean, y muchas gracias. 😌 
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
  <sub> Gracias por su atención al proyecto y por su apoyo 💙 </sub>
</p>


</details> 

* * * * * * * * * * * * * * * * * * 
* * * * * * * * * * * * * * * * * * 



<h2 align="center">
  <a href="#-contenido">📖 Guía de configuración del caché Unreal Engine 5.7</a>
</h2>

## 📚 Contenido

- [🎯 Introducción - Por qué es importante](#-introducción)
- [💾 Qué es un caché y qué almacena](#1-qué-se-cachéa-en-unreal-engine)
- [🏗️ Cómo funciona el sistema de caché](#2-arquitectura-de-ddc-en-ue-57)
- [📁 Configuración simple a través de carpeta compartida](#3-shared-ddc-a-través-de-filesystem)
- [☁️ Configuración avanzada con MinIO (mejor opción)](#4-shared-ddc-a-través-de-minio-opción-recomendada)
- [🤖 Automatización y CI/CD](#5-ddc-y-cicd-críticamente-importante)
- [📦 Almacenamiento de otros archivos del proyecto](#6-content-storage-no-solo-ddc)
- [🔢 Versiones de caché - Reglas importantes](#7-versionamiento-del-caché-error-común)
- [📊 Monitoreo y limpieza de archivos antiguos](#8-monitoreo-y-limpieza)
- [⚠️ Qué NO se debe cachéar](#9-qué-no-debería-ser-cachéado)
- [📈 Datos reales de mejora de tiempos](#10-resultados-en-cifras)
- [🛠️ Conjunto de herramientas mínimas necesarias](#11-conjunto-mínimo-recomendado)
- [📚 Materiales adicionales](#adicionalmente)

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

### 4.1 ¿Qué es MinIO y por qué se necesita?

**En términos simples** `MinIO` - es un software que convierte una carpeta común en almacenamiento en la nube (como AWS S3, pero privado).

<details> 
    <summary>⚙️ Ver descripción</summary>

**Analogía:**
- **Filesystem** = una carpeta habitual en la computadora
- **MinIO** = esa misma carpeta, pero con interfaz web y acceso desde internet

**¿Por qué MinIO es mejor que una carpeta común?**

| Función | Carpeta común | MinIO |
|---------|---------------|-------|
| Operar en red local | ✅ Sí | ✅ Sí |
| Operar a través de internet | ❌ Difícil | ✅ Fácil |
| Versionado de archivos | ❌ No | ✅ Sí |
| Limpieza automática de archivos antiguos | ❌ Manual | ✅ Automática |
| Monitoreo de uso | ❌ No | ✅ Sí |
| Escalabilidad | ❌ Baja | ✅ Excelente |
| Seguridad | ⚠️ Básica | ✅ Avanzada |

</details>

---

### 4.2 Arquitectura operativa

<details> 
    <summary>⚙️ Ver descripción</summary>

```
Desarrollador (Unreal Editor)
         ↓ Solicitud HTTP
      Servidor MinIO
         ↓
    Carpeta en disco
    /minio-data/
      ├─ ue-ddc-5-7/     ← Bucket (contenedor de archivos)
      │   ├─ Win64/
      │   └─ Linux/
```

**¿Qué es un Bucket?**
- `Bucket` = carpeta virtual dentro de MinIO. Es como un "contenedor" separado para almacenamiento de archivos.

</details>


### 4.3 Instalación de MinIO mediante Docker

<details> 
    <summary>⚙️ Ver descripción</summary>

#### ¿Por qué Docker?

- `Docker` = una forma de ejecutar un programa en un "contenedor" independiente del sistema.

**Ventajas:**
- ✅ Funciona igual en Windows, Linux, Mac
- ✅ No requiere instalar dependencias
- ✅ Fácil de eliminar si no gusta
- ✅ Aislado del resto del sistema

</details>

---


<h2 align="center">
  <a href="#-contenido">⬆️ Volver al contenido</a>
</h2>

---



---

# Paso 1: Instale Docker

<details> 
    <summary>⚙️ Windows </summary>

**Windows:**
1. Descargue [Docker Desktop](https://www.docker.com/products/docker-desktop)
2. Instálelo y ejecútelo
3. Verifique que Docker esté funcionando (ícono de una ballena en la bandeja del sistema)

</details>

<details> 
    <summary>⚙️ Linux (Ubuntu/Debian) </summary>

```bash
# Instalamos Docker
sudo apt update
sudo apt install docker.io docker-compose -y

# Agregamos el usuario al grupo docker
sudo usermod -aG docker $USER

# Reiniciamos
sudo reboot
```

**Verificación de instalación:**
```bash
docker --version
```
# Debería mostrar: `Docker version 24.0.x, build ...`

</details>

---
