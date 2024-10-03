#### Какие кластеры можно перенести в другую зону доступности? {#new-availability-zone}

Можно перенести [легковесные кластеры](../../data-proc/operations/migration-to-an-availability-zone.md) и [кластеры с файловой системой HDFS](../../data-proc/tutorials/hdfs-cluster-migration.md).

#### Что делать, если данные на хостах подкластера для хранения данных распределены неравномерно? {#data-unevenly-distributed}

[Подключитесь](../../data-proc/operations/connect.md) к хосту-мастеру кластера и запустите перебалансировку с помощью команды:

```bash
sudo -u hdfs hdfs balancer
```

Вы можете изменять параметры балансировщика. Например, чтобы изменить максимальный объем данных для переноса, добавьте аргумент `-D dfs.balancer.max-size-to-move=<размер-данных-в-байтах>`.

#### Где можно посмотреть логи кластера {{ dataproc-name }}? {#cluster-logs}

Логи кластера доступны в его лог-группе. Чтобы следить за событиями кластера и его отдельных хостов, при [создании](../../data-proc/operations/cluster-create.md) или [изменении](../../data-proc/operations/cluster-update.md) кластера укажите в его настройках нужную [лог-группу](../../logging/concepts/log-group.md). Если для кластера не выбрана лог-группа, то для отправки и хранения логов будет использована лог-группа по умолчанию в каталоге кластера. Подробнее см. в разделе [{#T}](../../data-proc/operations/logging.md).

{% include [logs](../logs.md) %}

#### Почему кластер работает медленно, хотя вычислительные ресурсы использованы не до предела? {#throttling}

{% include [throttling](../throttling.md) %}

Чтобы увеличить максимальные значения IOPS и bandwidth и снизить вероятность троттлинга, рассмотрите возможность перехода на другой кластер с увеличенным размером хранилищ на хостах или более быстрым типом дисков. Перенести данные на новый кластер можно, например, при помощи [{{ metastore-full-name }}](../../metadata-hub/concepts/metastore.md).

#### При запуске скрипта инициализации я получаю ошибку `^M: bad interpreter`, как это исправить? {#syntax-error}

Так как среда выполнения скрипта — Linux (Ubuntu), то скрипты, подготовленные в Windows, могут выполниться с ошибкой `^M: bad interpreter` из-за использования символа переноса строки `CR/LF` (в Linux – `LF`). Для исправления ошибки сохраните файл скрипта в формате Linux. Подробнее см. в разделе [{#T}](../../data-proc/concepts/init-action.md#syntax-errors).

#### При запуске задания PySpark возникает ошибка, связанная с `com/amazonaws/auth/AWSCredentialsProvider`, как это исправить? {#sharedPrefixes-property}

Если кластер {{ dataproc-name }} подключен к кластеру {{ metastore-name }}, при запуске PySpark-заданий может возникать ошибка:

```text
previously initiated loading for a different type with name "com/amazonaws/auth/AWSCredentialsProvider";
```

Чтобы ее исправить, [добавьте свойство](../../data-proc/operations/cluster-update.md) `spark:spark.sql.hive.metastore.sharedPrefixes` со значением `com.amazonaws,ru.yandex.cloud` в кластер {{ dataproc-name }}.

#### Почему возникает ошибка `NAT should be enabled on the subnet`, и как ее исправить? {#nat}

Ошибка возникает при попытке создать кластер {{ dataproc-name }} в подсети, для которой не настроен NAT-шлюз. Чтобы исправить ее, [настройте сеть для {{ dataproc-name }}](../../data-proc/tutorials/configure-network.md).

#### Почему возникает ошибка `Using fileUris is forbidden on lightweight cluster`, и как ее исправить? {#file-uri}

Ошибка возникает из-за того, что конфигурация [легковесных кластеров](../../data-proc/concepts/index.md#light-weight-clusters) не включает в себя HDFS. Чтобы исправить ошибку, [создайте кластер](../../data-proc/operations/cluster-create.md), который поддерживает HDFS.

Кроме того, для работы с заданиями (jobs) рекомендуется использовать [бакеты {{ objstorage-full-name }}](../../storage/concepts/bucket.md). Вы можете [загружать в них скрипты](../../storage/operations/objects/upload.md) для запуска заданий. Эти скрипты хранятся в виде объектов, на которые можно [получать ссылки](../../storage/operations/objects/link-for-download.md). В результате вместо ссылки вида `file:/` вы можете использовать ссылки из {{ objstorage-name }} в своих заданиях.

#### Почему возникает ошибка `Create {{ dataproc-name }} cluster Error: 0 Address space exhausted`, и как ее исправить? {#addresses-exhausted}

Ошибка означает, что закончились свободные IP-адреса в подсети кластера {{ dataproc-name }}, которые можно выделить хостам кластера. Чтобы проверить количество доступных адресов, [посмотрите список используемых адресов](../../vpc/operations/subnet-used-addresses.md) в подсети и ее маску.

Чтобы исправить ошибку, выполните одно из действий:

* Удалите лишние ресурсы, которые занимают IP-адреса в подсети.
* Создайте подсеть с CIDR, подходящим под конфигурацию вашего кластера. Затем создайте кластер {{ dataproc-name }} в новой подсети.

Подробнее о размерах подсетей см. в документации [{{ vpc-full-name }}](../../vpc/concepts/network.md#subnet).

#### Почему кластер в статусе `Unknown`? {#unknown}

Если у кластера был статус `Alive`, а затем стал `Unknown`:

1. Убедитесь, что вы [настроили сеть для {{ dataproc-name }}](../../data-proc/tutorials/configure-network.md). Для работы кластера нужно создать и настроить несколько сетевых ресурсов:

   * сеть;
   * подсеть;
   * NAT-шлюз;
   * таблицу маршрутизации;
   * группу безопасности;
   * сервисный аккаунт для кластера;
   * бакет, в котором будут храниться зависимости заданий и результаты их выполнения.

1. Изучите логи, которые описывают состояние кластера за заданный период:

   ```bash
   yc logging read \
      --group-id=<идентификатор_лог-группы> \
      --resource-ids=<идентификатор_кластера> \
      --filter=log_type=yandex-dataproc-agent \
      --since 'YYYY-MM-DDThh:mm:ssZ' \
      --until 'YYYY-MM-DDThh:mm:ssZ'
   ```

   В параметрах `--since` и `--until` укажите границы периода. Формат времени: `YYYY-MM-DDThh:mm:ssZ`. Пример: `2020-08-10T12:00:00Z`. Время указывается в часовом поясе UTC.

   Подробнее см. в разделе [{#T}](../../data-proc/operations/logging.md).

#### Какая минимальная вычислительная мощность нужна для работы подкластера с хостом-мастером? {#master-computing-power}

Это зависит от режима размещения драйвера:

{% include [subcluster-computing-nodes](../../_includes/data-proc/subcluster-computing-nodes.md) %}

В {{ yandex-cloud }} вычислительная мощность определяется классом хостов. Их соотношение см. в разделе [Классы хостов](../../data-proc/concepts/instance-types.md).

#### Как обновить версию образа в {{ dataproc-name }}? {#upgrade}

В сервисе нет встроенного механизма для обновления [версии образа](../../data-proc/concepts/environment.md). Чтобы ее обновить, создайте новый кластер.

Если вы хотите постоянно использовать актуальную версию, [автоматизируйте](../../data-proc/tutorials/airflow-automation.md) создание и удаление временных кластеров {{ dataproc-name }} с помощью сервиса {{ maf-full-name }}. Для автоматизированного запуска заданий помимо {{ maf-name }} вы также можете [использовать](../../data-proc/tutorials/datasphere-integration.md) сервис {{ ml-platform-full-name }}.

#### Как запустить задания (jobs)? {#jobs}

Возможно несколько способов:

{% include [running-jobs](../../_includes/data-proc/running-jobs.md) %}

#### Какие есть лимиты на группы безопасности? {#security-groups}

В сети можно создавать не более пяти групп безопасности. В каждой группе может быть до 50 правил. Подробнее о [лимитах в {{ vpc-full-name }}](../../vpc/concepts/limits.md#vpc-limits).

#### Можно ли получить на хостах права суперпользователя? {#connect-root}

Да. Чтобы переключиться на суперпользователя, после подключения к хосту введите команду:

   ```bash
     sudo su
   ```

Однако переключаться на суперпользователя необязательно — достаточно использовать `sudo`.