---
title: "История изменений в {{ mkf-full-name }}"
description: "В разделе представлена история изменений сервиса {{ mkf-name }}."
---

# История изменений в {{ mkf-full-name }}

{% include [Tags](../_includes/mdb/release-notes-tags.md) %}

## Июль 2024 {#jule-2024}

Добавлено автоматическое увеличение размера диска. В [настройках кластера](./operations/cluster-update.md) пользователь может задать порог заполненности дискового хранилища и максимальное значение размера диска. При достижении порогового значения размер диска будет автоматически увеличиваться фиксированными шагами до максимального значения. Настройку можно задать для незамедлительного увеличения размера диска или для увеличения в ближайшее окно обслуживания.

## Март 2024 {#mar-2024}

Добавлена возможность [замены зоны доступности](./operations/host-migration.md) для кластеров {{ mkf-name }}.

## IV квартал 2023 {#q4-2023}

Новая версия {{ KF }} 3.5.1 доступна в окружении `PRODUCTION`. Подробнее об изменениях см. в [документации {{ KF }} 3.5.0](https://archive.apache.org/dist/kafka/3.5.0/RELEASE_NOTES.html) и [документации {{ KF }} 3.5.1](https://archive.apache.org/dist/kafka/3.5.1/RELEASE_NOTES.html). {{ tag-con }} {{ tag-cli }} {{ tag-tf }}

## II квартал 2023 {#q2-2023}

Новая версия {{ KF }} 3.4 доступна в окружении `PRODUCTION`. Подробнее об изменениях см. в [документации {{ KF }}](https://archive.apache.org/dist/kafka/3.4.0/RELEASE_NOTES.html). {{ tag-con }} {{ tag-cli }} {{ tag-tf }}

## I квартал 2023 {#q1-2023}

Новая версия {{ KF }} 3.3 доступна в окружении `PRODUCTION`. {{ tag-con }} {{ tag-cli }} {{ tag-tf }}

## IV квартал 2022 {#q4-2022}

* Добавлена поддержка настройки [Sasl enabled mechanisms](concepts/settings-list.md#settings-sasl-enabled-mechanisms), позволяющей задать доступные при подключении к кластеру механизмы шифрования.
* Для пользователей с ролью `admin` добавлена возможность удалять группы потребителей (consumer groups).
* Коннектор [S3 Sink](concepts/connectors.md#s3-sink) теперь доступен в CLI. {{ tag-cli }}

## III квартал 2022 {#q3-2022}

* Добавлена поддержка управления коннекторами в CLI с помощью команды `{{ yc-mdb-kf }} connector` и коннекторами типа MirrorMaker с помощью команды `{{ yc-mdb-kf }} connector-mirrormaker`. {{ tag-cli }}
* Ускорены операции по изменению прав пользователей при большом количестве топиков.
* Добавлена поддержка [настроек](concepts/settings-list.md#cluster-settings) `Message max bytes`, `Offsets retention minutes`, `Replica fetch max bytes` и `Ssl cipher suites`.
* Добавлена возможность создания кластера на локальных дисках на платформе Intel Ice Lake.
* Исправлен расчет метрики `kafka_group_topic_partition_lag`.  Подробнее см. в [справочнике метрик {{ monitoring-full-name }}](../_includes/monitoring/metrics-ref/managed-kafka.md). 
* Новая версия {{ KF }} 3.2 доступна в окружении `PRODUCTION`. {{ tag-con }} {{ tag-cli }} {{ tag-tf }}

## II квартал 2022 {#q2-2022}

* Доступен новый коннектор: [S3 Sink](concepts/connectors.md#s3-sink). {{ tag-con }}
* Новая версия {{ KF }} 3.1 доступна в окружениях `PRESTABLE` и `PRODUCTION`. {{ tag-con }} {{ tag-cli }} {{ tag-tf }}
* Добавлена возможность загружать SSL-сертификат для соединения с кластером через коннектор MirrorMaker. {{ tag-con }}
* Добавлена поддержка офлайн-обслуживания.
* Добавлена возможность изменять настройку публичного доступа в CLI. {{ tag-cli }}.
* Запрещено использование флага `preallocate`, провоцирующего `CorruptRecordException` (см. тикет [KAFKA-13664](https://issues.apache.org/jira/browse/KAFKA-13664)).
* Добавлена поддержка стандартного сжатия (zstd) для реестра схем (schema registry).

## I квартал 2022 {#q1-2022}

Доступна новая версия {{ KF }} 3.0.
