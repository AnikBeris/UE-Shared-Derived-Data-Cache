# Unreal Engine 5.7 – Shared Derived Data Cache (DDC) и Content Storage

Данный документ описывает архитектуру, назначение и практическую реализацию **Shared Derived Data Cache (DDC)** и **Content Storage** для Unreal Engine 5.7 с использованием Docker и MinIO. Материал ориентирован на индивидуальных разработчиков и небольшие команды, а также масштабируется под CI/CD.

---

## 1. Что именно кешируется в Unreal Engine

В Unreal Engine существует несколько типов данных, которые критично влияют на время сборки, запуска проекта и CI-пайплайнов.

### 1.1 Derived Data Cache (DDC) – ключевой элемент

**DDC (Derived Data Cache)** – это кеш всех производных данных, которые UE генерирует из исходного контента.

Кешируются:

- скомпилированные шейдеры
- обработанные текстуры (mipmaps, компрессия)
- cooked assets
- данные Nanite и Lumen
- Niagara shader cache
- Virtual Texture tiles

#### Без общего DDC

- каждый разработчик компилирует шейдеры локально
- каждая CI-сборка пересобирает данные с нуля
- старт проекта после `git pull` может занимать десятки минут

#### С общим DDC

- компиляция шейдеров выполняется один раз
- остальные машины скачивают готовые данные
- ускорение работы в **10–30 раз**

---

## 2. Архитектура DDC в UE 5.7

Unreal Engine поддерживает многоуровневую архитектуру кеша:

```
Local DDC (SSD разработчика)
   ↓
Shared DDC (LAN / HTTP / S3)
   ↓
Fallback (опционально)
```

### Поддерживаемые backend-типы

- Filesystem (SMB / NFS)
- HTTP backend
- S3-compatible backend
- Zen (внутренний Epic, частично доступен)

Для Docker-инфраструктуры оптимальны:

- Filesystem DDC
- S3-compatible DDC (через MinIO)

---

## 3. Shared DDC через Filesystem

### 3.1 Когда использовать

- небольшая команда
- все разработчики в одной локальной сети
- NAS или сервер с быстрым SSD/NVMe

### 3.2 Реализация

Создается общий каталог, например:

```
/mnt/ddc
```

Этот путь:

- монтируется по SMB/NFS на машины разработчиков
- монтируется в CI контейнеры

Пример конфигурации `Engine.ini`:

```ini
[DerivedDataBackendGraph]
Shared=(Type=FileSystem, Path=//server/DDC, WriteAccess=true)
```

### Плюсы

- минимальная сложность
- высокая скорость в LAN
- отсутствие дополнительного оверхеда

### Минусы

- плохая масштабируемость
- зависимость от сетевой файловой системы
- отсутствие версионирования

---

## 4. Shared DDC через MinIO (рекомендуемый вариант)

### 4.1 Почему MinIO

MinIO – это S3-compatible object storage, идеально подходящий для DDC:

- работает по HTTP
- легко масштабируется
- отлично разворачивается в Docker
- позволяет изолировать кеши по версиям UE и платформам

---

### 4.2 Архитектура

```
UE Editor / CI
   ↓ HTTP
MinIO (S3)
   ↓
NVMe / HDD
```

---

### 4.3 Пример Docker Compose

```yaml
services:
  minio:
    image: minio/minio
    command: server /data --console-address ":9001"
    volumes:
      - ./minio-data:/data
    environment:
      MINIO_ROOT_USER=ddc
      MINIO_ROOT_PASSWORD=ddcpassword
    ports:
      - "9000:9000"
      - "9001:9001"
```

---

### 4.4 Конфигурация UE 5.7

```ini
[DerivedDataBackendGraph]
Shared=(Type=S3,
        Bucket=ue-ddc-5-7,
        Region=us-east-1,
        AccessKey=ddc,
        SecretKey=ddcpassword,
        Endpoint=http://minio:9000,
        WriteAccess=true)
```

---

### Лучшие практики

- отдельный bucket:
  - на каждую версию UE
  - на каждую платформу (Win64, Linux, Android)
- CI runners использовать в режиме `ReadOnly`
- отдельный runner для прогрева кеша
- ночные warmup-сборки

---

## 5. DDC и CI/CD (критически важно)

### 5.1 Типовая проблема

- каждый CI pipeline компилирует шейдеры заново
- сборки могут занимать 2–4 часа

### 5.2 Решение

1. Один CI job:
   - запускается по расписанию
   - выполняет заполнение DDC

2. Остальные jobs:
   - используют кеш в режиме read-only
   - сборки становятся стабильными и быстрыми

Пример запуска:

```bash
UEEditor-Cmd.exe Project.uproject -run=DerivedDataCache -fill
```

---

## 6. Content Storage (не только DDC)

### 6.1 Cooked builds

- результаты `RunUAT BuildCookRun`
- хранятся в MinIO или Nexus
- используются для:
  - QA
  - regression testing
  - nightly builds

### 6.2 Плагины Unreal Engine

- бинарные плагины
- source-плагины
- зеркала Marketplace (при соблюдении лицензий)

### 6.3 SDK и Toolchains

- Android SDK / NDK
- iOS toolchains
- сторонние middleware

---

## 7. Версионирование кеша (частая ошибка)

Никогда не используйте один DDC для разных версий:

- UE 5.3 и UE 5.7
- разные commit-хеши engine
- разные RHI

### Корректная структура bucket-ов

```
ue-ddc/
 ├─ 5.7/
 │   ├─ win64/
 │   └─ linux/
 ├─ 5.8/
```

---

## 8. Мониторинг и очистка

### 8.1 Метрики

- объем бакетов
- hit/miss ratio
- latency

Рекомендуемый стек:
- MinIO
- Prometheus
- Grafana

### 8.2 Очистка

- TTL для старых объектов
- lifecycle rules MinIO
- очистка по версиям проекта

---

## 9. Что не стоит кешировать

- `Intermediate/`
- `Saved/`
- `Binaries/` (кроме релизных сборок)

---

## 10. Результат в цифрах

| Операция | Без DDC | Shared DDC |
|--------|--------|-----------|
| Первый запуск проекта | 30–60 мин | 5–10 мин |
| CI билд | 2–4 часа | 20–40 мин |
| Новый разработчик | ~1 день | ~1 час |

---

## 11. Рекомендуемый минимальный стек

Минимально разумная инфраструктура:

- MinIO (DDC + cooked builds)
- GitLab Runner (warmup job)
- Prometheus + Grafana
- SSD/NVMe хранилище

---

## Дополнительно

При необходимости можно:
- адаптировать `Engine.ini` под конкретный проект UE 5.7
- построить полноценный CI pipeline с warmup DDC
- масштабировать архитектуру под команды 1–3 / 5–10 / 20+ человек
