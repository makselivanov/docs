---
title: "История изменений в {{ serverless-containers-full-name }}"
description: "В разделе представлена история изменений сервиса {{ serverless-containers-name }}."
---

# История изменений в {{ serverless-containers-full-name }}

## Апрель 2024 {#april-2024}

### Исправления и улучшения {#fixes-improvements}

* Удалено требование для [пользовательской сети](concepts/networking#user-network) иметь подсеть в зоне доступности `ru-central1-c` в связи с [выводом этой зоны из эксплуатации](../overview/concepts/ru-central1-c-deprecation).

## Март 2024 {#march-2024}

### Обновления {#updates}

* Добавлена поддержка редактирования всех параметров триггеров в {{ TF }}.
* [Монтирование бакетов {{ objstorage-full-name }}](concepts/mounting.md) в контейнер перешло на [стадию General Availability](../overview/concepts/launch-stages.md).

### Исправления и улучшения {#fixes-improvements}

* Максимальный размер группы в [триггере для {{ message-queue-full-name }}](concepts/trigger/ymq-trigger.md) увеличен до 1000 сообщений.

## Январь — февраль 2024 {#jan-feb-2024}

### Обновления {#updates}

* Добавлена поддержка настроек логирования для контейнера в {{ TF }}.
* Добавлена поддержка [монтирования бакетов {{ objstorage-full-name }}](concepts/mounting.md) в контейнер в CLI и {{ TF }}.
