# Просмотр логов кластера {{ PG }}

{{ mpg-name }} позволяет [получить фрагмент логов кластера](#get-log) за выбранный период и [просматривать логи в реальном времени](#get-log-stream).

{% include [log-duration](../../_includes/mdb/log-duration.md) %}

Чтобы обнаружить возможные проблемы в кластере, [используйте другие инструменты](../tutorials/performance-problems.md) для анализа состояния кластера вместе с его логами.

## Получить лог кластера {#get-log}

{% list tabs group=instructions %}

- Консоль управления {#console}

    1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку ![image](../../_assets/console-icons/receipt.svg) **{{ ui-key.yacloud.postgresql.cluster.switch_logs }}**.
    1. Укажите период времени, за который нужно отобразить логи: введите его вручную или выберите в календаре, нажав на поле ввода дат.
    1. При необходимости запросите лог `POOLER`, укажите хосты и уровень логирования в строке с полем ввода дат.

    Будет отображен список записей в логе за выбранный период времени. Чтобы посмотреть подробную информацию о событии, нажмите на интересующую запись в списке.

    Если записей слишком много и отображается только часть из них, нажмите на кнопку **{{ ui-key.yacloud.common.label_load-more }}** в конце списка.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    1. Просмотрите описание команды CLI для просмотра логов кластера:

        ```bash
        {{ yc-mdb-pg }} cluster list-logs --help
        ```

    1. Запустите команду получения логов кластера (в примере приведены не все доступные параметры):

        ```bash
        {{ yc-mdb-pg }} cluster list-logs <имя_или_идентификатор_кластера> \
           --limit <ограничение_количества_записей> \
           --format <формат_вывода> \
           --service-type <тип_сервиса> \
           --columns <список_колонок_для_вывода_информации> \
           --filter <настройки_фильтрации_записей> \
           --since <левая_граница_временного_диапазона> \
           --until <правая_граница_временного_диапазона>
        ```

        Где:

        * {% include [logs output limit](../../_includes/cli/logs/limit.md) %}
        * {% include [logs output format](../../_includes/cli/logs/format.md) %}
        * `--service-type` — тип сервиса, для которого требуется вывести записи (`postgresql` или `pooler`).
        * `--columns` — список колонок для вывода информации:
            * `hostname` — [имя хоста](hosts.md#list-hosts).
            * `db` — [имя базы данных](databases.md#list-db).
            * `level` — уровень логирования, например, `info`.
            * `pid` — идентификатор серверного процесса текущей сессии.
            * `text` — сообщение, которое выводит компонент.

            {% note info %}

            В примере приведены только основные колонки. Список выводимых колонок зависит от выбранного типа сервиса `--service-type`.

            {% endnote %}

            {% include [logs column format](../../_includes/cli/logs/column-format.md) %}

        * {% include [logs filter](../../_includes/cli/logs/filter.md) %}
        * {% include [logs since time](../../_includes/cli/logs/since.md) %}
        * {% include [logs until time](../../_includes/cli/logs/until.md) %}

    Имя и идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- REST API {#api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. Воспользуйтесь методом [Cluster.listLogs](../api-ref/Cluster/listLogs.md) и выполните запрос, например, с помощью {{ api-examples.rest.tool }}:

     ```bash
     curl \
       --request GET \
       --header "Authorization: Bearer $IAM_TOKEN" \
       --url 'https://{{ api-host-mdb }}/managed-postgresql/v1/clusters/<идентификатор_кластера>:logs?serviceType=<тип_сервиса>&columnFilter=<список_колонок>&fromTime=<левая_граница_временного_диапазона>&toTime=<правая_граница_временного_диапазона>'
     ```

     Где:

     * `serviceType` — тип сервиса, логи которого нужно получить:

       * `POSTGRESQL` — логи операций {{ PG }};
       * `POOLER` — логи операций менеджера подключений.

     * `columnFilter` — список колонок, информацию по которым нужно вывести. В качестве значений используйте поля объекта `message` из ответа на запрос.
     * `fromTime` — левая граница временного диапазона в формате [RFC-3339](https://www.ietf.org/rfc/rfc3339.html). Пример: `2024-09-18T15:04:05Z`.
     * `toTime` — правая граница временного диапазона, формат аналогичен `fromTime`.

     Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/listLogs.md#responses).

- gRPC API {#grpc-api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}
  1. Воспользуйтесь вызовом [ClusterService/ListLogs](../api-ref/grpc/cluster_service.md#ListLogs) и выполните запрос, например, с помощью {{ api-examples.grpc.tool }}:

     ```bash
     grpcurl \
       -format json \
       -import-path ~/cloudapi/ \
       -import-path ~/cloudapi/third_party/googleapis/ \
       -proto ~/cloudapi/yandex/cloud/mdb/postgresql/v1/cluster_service.proto \
       -rpc-header "Authorization: Bearer $IAM_TOKEN" \
       -d '{
             "cluster_id": "<идентификатор_кластера>",
             "service_type": "<тип_сервиса>",
             "column_filter": [
               "<колонка_1>", "<колонка_2>", ..., "<колонка_N>"
             ],
             "from_time": "<левая_граница_временного_диапазона>",
             "to_time": "<правая_граница_временного_диапазона>"
           }' \
       {{ api-host-mdb }}:{{ port-https }} \
       yandex.cloud.mdb.postgresql.v1.ClusterService.ListLogs
     ```

     Где:

     * `service_type` — тип сервиса, логи которого нужно получить:

       * `POSTGRESQL` — логи операций {{ PG }};
       * `POOLER` — логи операций менеджера подключений.

     * `column_filter` — массив колонок, информацию по которым нужно вывести. Массив состоит из строк, каждая из которых содержит название колонки. В качестве значений используйте поля объекта `message` из ответа на запрос.
     * `from_time` — левая граница временного диапазона в формате [RFC-3339](https://www.ietf.org/rfc/rfc3339.html). Пример: `2024-09-18T15:04:05Z`.
     * `to_time` — правая граница временного диапазона, формат аналогичен `from_time`.

     Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/cluster_service.md#ListClusterLogsResponse).

{% endlist %}

## Получить поток логов кластера {#get-log-stream}

Этот способ позволяет получать логи кластера в реальном времени.

{% list tabs group=instructions %}

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Для просмотра логов кластера по мере их поступления выполните команду:

    ```bash
    {{ yc-mdb-pg }} cluster list-logs <имя_или_идентификатор_кластера> --follow
    ```

    Имя и идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- REST API {#api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. Воспользуйтесь методом [Cluster.streamLogs](../api-ref/Cluster/streamLogs.md) и выполните запрос, например, с помощью {{ api-examples.rest.tool }}:

     ```bash
     curl \
       --request GET \
       --header "Authorization: Bearer $IAM_TOKEN" \
       --url 'https://{{ api-host-mdb }}/managed-postgresql/v1/clusters/<идентификатор_кластера>:stream_logs?serviceType=<тип_сервиса>&columnFilter=<список_колонок>'
     ```

     Где:

     * `serviceType` — тип сервиса, логи которого нужно получить:

       * `POSTGRESQL` — логи операций {{ PG }};
       * `POOLER` — логи операций менеджера подключений.

     * `columnFilter` — список колонок, информацию по которым нужно вывести. В качестве значений используйте поля объекта `message` из ответа на запрос.

     Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/streamLogs.md#responses).

     Команда не завершается после отправки. Новые логи отображаются в выводе команды в режиме реального времени.

- gRPC API {#grpc-api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}
  1. Воспользуйтесь вызовом [ClusterService/StreamLogs](../api-ref/grpc/cluster_service.md#StreamLogs) и выполните запрос, например, с помощью {{ api-examples.grpc.tool }}:

     ```bash
     grpcurl \
       -format json \
       -import-path ~/cloudapi/ \
       -import-path ~/cloudapi/third_party/googleapis/ \
       -proto ~/cloudapi/yandex/cloud/mdb/postgresql/v1/cluster_service.proto \
       -rpc-header "Authorization: Bearer $IAM_TOKEN" \
       -d '{
             "cluster_id": "<идентификатор_кластера>",
             "service_type": "<тип_сервиса>",
             "column_filter": [
               "<колонка_1>", "<колонка_2>", ..., "<колонка_N>"
             ]
           }' \
       {{ api-host-mdb }}:{{ port-https }} \
       yandex.cloud.mdb.postgresql.v1.ClusterService.StreamLogs
     ```

     Где:

     * `service_type` — тип сервиса, логи которого нужно получить:

       * `POSTGRESQL` — логи операций {{ PG }};
       * `POOLER` — логи операций менеджера подключений.

     * `column_filter` — массив колонок, информацию по которым нужно вывести. Массив состоит из строк, каждая из которых содержит название колонки. В качестве значений используйте поля объекта `message` из ответа на запрос.
     * `from_time` — левая граница временного диапазона в формате [RFC-3339](https://www.ietf.org/rfc/rfc3339.html). Пример: `2024-09-18T15:04:05Z`.
     * `to_time` — правая граница временного диапазона, формат аналогичен `from_time`.

     Идентификатор кластера можно запросить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/cluster_service.md#StreamLogRecord).

     Команда не завершается после отправки. Новые логи отображаются в выводе команды в режиме реального времени.

{% endlist %}
