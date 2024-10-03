---
title: "Информация о кластерах в {{ mch-short-name }}"
description: "Вы можете запросить детальную информацию о каждом созданном вами кластере {{ mch-short-name }}. Чтобы получить список кластеров БД в каталоге, перейдите на страницу каталога и выберите сервис {{ mch-name }}."
---

# Информация об имеющихся кластерах {{ CH }}

Вы можете запросить детальную информацию о каждом созданном вами кластере {{ mch-short-name }}.

## Получить список кластеров БД в каталоге {#list-clusters}

{% list tabs group=instructions %}

- Консоль управления {#console}

  В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы запросить список кластеров {{ CH }} в каталоге по умолчанию, выполните команду:

  ```
  {{ yc-mdb-ch }} cluster list

  +----------------------+------+-------------+---------+---------------------+--------+---------+
  |          ID          | NAME | ENVIRONMENT | VERSION |     CREATED AT      | HEALTH | STATUS  |
  +----------------------+------+-------------+---------+---------------------+--------+---------+
  | c9qf1kmm0ebi******** | mych | PRODUCTION  |    23.8 | 2024-06-06 10:23:22 | ALIVE  | RUNNING |
  | ...                                                                                          |
  +----------------------+------+-------------+---------+---------------------+--------+---------+
  ```

- REST API {#api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

      {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. Воспользуйтесь методом [Cluster.list](../api-ref/Cluster/list.md) и выполните запрос, например, с помощью {{ api-examples.rest.tool }}:

      ```bash
      curl \
          --request GET \
          --header "Authorization: Bearer $IAM_TOKEN" \
          --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters?folderId=<идентификатор_каталога>'
      ```

      
      Идентификатор каталога можно запросить со [списком каталогов в облаке](../../resource-manager/operations/folder/get-id.md).


  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/list.md#responses).

- gRPC API {#grpc-api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

      {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

  1. Воспользуйтесь вызовом [ClusterService/List](../api-ref/grpc/cluster_service.md#List) и выполните запрос, например, с помощью {{ api-examples.grpc.tool }}:

      ```bash
      grpcurl \
          -format json \
          -import-path ~/cloudapi/ \
          -import-path ~/cloudapi/third_party/googleapis/ \
          -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/cluster_service.proto \
          -rpc-header "Authorization: Bearer $IAM_TOKEN" \
          -d '{
                  "folder_id": "<идентификатор_каталога>"
              }' \
          {{ api-host-mdb }}:443 \
          yandex.cloud.mdb.clickhouse.v1.ClusterService.List
      ```

      
      Идентификатор каталога можно запросить со [списком каталогов в облаке](../../resource-manager/operations/folder/get-id.md).


  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/cluster_service.md#ListClustersResponse).

{% endlist %}

## Получить детальную информацию о кластере {#get-cluster}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Нажмите на имя нужного кластера.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы получить информацию о кластере {{ CH }}, выполните команду:

  ```
  {{ yc-mdb-ch }} cluster get <имя_или_идентификатор_кластера>
  ```

  Идентификатор и имя кластера можно запросить со [списком кластеров в каталоге](#list-clusters).

- REST API {#api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

      {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. Воспользуйтесь методом [Cluster.get](../api-ref/Cluster/get.md) и выполните запрос, например, с помощью {{ api-examples.rest.tool }}:

      ```bash
      curl \
          --request GET \
          --header "Authorization: Bearer $IAM_TOKEN" \
          --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<идентификатор_кластера>'
      ```

      Идентификатор кластера можно запросить со [списком кластеров в каталоге](#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/get.md#responses).

- gRPC API {#grpc-api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

      {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

  1. Воспользуйтесь вызовом [ClusterService/Get](../api-ref/grpc/cluster_service.md#Get) и выполните запрос, например, с помощью {{ api-examples.grpc.tool }}:

      ```bash
      grpcurl \
          -format json \
          -import-path ~/cloudapi/ \
          -import-path ~/cloudapi/third_party/googleapis/ \
          -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/cluster_service.proto \
          -rpc-header "Authorization: Bearer $IAM_TOKEN" \
          -d '{
                  "cluster_id": "<идентификатор_кластера>"
              }' \
          {{ api-host-mdb }}:443 \
          yandex.cloud.mdb.clickhouse.v1.ClusterService.Get
      ```

      Идентификатор кластера можно запросить со [списком кластеров в каталоге](#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/cluster_service.md#Cluster).

{% endlist %}

## Посмотреть операции с кластерами {#list-operations}

Все действия с кластерами {{ mch-name }} сохраняются в виде списка операций. Каждой операции присваивается уникальный идентификатор.

### Получить список операций {#get-operations}

{% list tabs group=instructions %}

- Консоль управления {#console}

  Чтобы посмотреть операции со всеми кластерами {{ mch-name }}, на панели слева выберите ![image](../../_assets/console-icons/list-check.svg) **{{ ui-key.yacloud.clickhouse.switch_operations }}**. В открывшемся списке также отображаются операции для ресурсов, которые были удалены.

  Можно получить список операций для кластера:

  1. В [консоли управления]({{ link-console-main }}) откройте каталог, в котором находится кластер.
  1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. На панели слева выберите ![image](../../_assets/console-icons/cubes-3.svg) **{{ ui-key.yacloud.clickhouse.switch_list }}**.
  1. Выберите нужный кластер.
  1. Перейдите на панель ![image](../../_assets/console-icons/list-check.svg) **{{ ui-key.yacloud.clickhouse.switch_operations }}** для выбранного кластера.

     В открывшемся списке отображаются операции с выбранным кластером.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы получить список операций для кластера {{ mch-name }}, воспользуйтесь командой:

  ```bash
  yc managed-clickhouse cluster list-operations <имя_или_идентификатор_кластера>
  ```

  Результат:

  ```text
  +----------------------+---------------------+----------------------+---------------------+--------+--------------------------------+
  |          ID          |     CREATED AT      |      CREATED BY      |     MODIFIED AT     | STATUS |          DESCRIPTION           |
  +----------------------+---------------------+----------------------+---------------------+--------+--------------------------------+
  | c5m7ll6pq3j8******** | 2024-08-08 19:05:42 | mdb-maintainer       | 2024-08-15 19:20:05 | DONE   | Modify ClickHouse cluster      |
  | c5mb0no66172******** | 2024-06-19 12:48:40 | mdb-maintainer       | 2024-06-26 12:50:14 | DONE   | Modify ClickHouse cluster      |
  | c5m9hvmb3lg9******** | 2024-01-17 08:05:16 | ajeef73j5iq9******** | 2024-01-17 08:14:59 | DONE   | Add ZooKeeper to ClickHouse    |
  |                      |                     |                      |                     |        | cluster                        |
  | c5mniao7leml******** | 2023-11-13 19:34:54 | ajeef73j5iq9******** | 2023-11-13 19:35:10 | DONE   | Update user in ClickHouse      |
  |                      |                     |                      |                     |        | cluster                        |
  | c5m6of9he7op******** | 2023-11-13 19:25:21 | ajeef73j5iq9******** | 2023-11-13 19:32:00 | DONE   | Create ClickHouse cluster      |
  +----------------------+---------------------+----------------------+---------------------+---- ---+--------------------------------+
  ```

  Идентификатор и имя кластера можно получить, запросив [список кластеров в каталоге](#list).

  По умолчанию информация об операциях выводится в текстовом формате. Чтобы получить более подробную информацию, укажите формат `yaml` или `json` для выводимых данных с помощью флага `--format`:

  ```bash
  yc managed-clickhouse cluster list-operations epdplu8jn7sr******** --format yaml
  ```

  Результат:

  ```text
  - id: c5m7ll6pq3j8********
    description: Modify ClickHouse cluster
    created_at: "2024-08-08T19:05:42.362668Z"
    created_by: mdb-maintainer
    modified_at: "2024-08-15T19:20:05.917268Z"
    done: true
    metadata:
      '@type': type.googleapis.com/yandex.cloud.mdb.clickhouse.v1.UpdateClusterMetadata
      cluster_id: c9q7vuuoiqml********
    response:
      '@type': type.googleapis.com/google.rpc.Status
      message: OK
  ...
  ```

- REST API {#api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

     {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. Воспользуйтесь методом [Cluster.listOperations](../api-ref/Cluster/listOperations.md) и выполните запрос, например, с помощью {{ api-examples.rest.tool }}:

      ```bash
      curl \
          --request GET \
          --header "Authorization: Bearer $IAM_TOKEN" \
          --url 'https://{{ api-host-mdb }}/managed-clickhouse/v1/clusters/<идентификатор_кластера>/operations'
      ```

      Идентификатор кластера можно получить со [списком кластеров в каталоге](#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/Cluster/listOperations.md#responses).

- gRPC API {#grpc-api}

  1. [Получите IAM-токен для аутентификации в API](../api-ref/authentication.md) и поместите токен в переменную среды окружения:

      {% include [api-auth-token](../../_includes/mdb/api-auth-token.md) %}

  1. {% include [grpc-api-setup-repo](../../_includes/mdb/grpc-api-setup-repo.md) %}

  1. Воспользуйтесь вызовом [ClusterService/ListOperations](../api-ref/grpc/cluster_service.md#ListOperations) и выполните запрос, например, с помощью {{ api-examples.grpc.tool }}:

      ```bash
      grpcurl \
          -format json \
          -import-path ~/cloudapi/ \
          -import-path ~/cloudapi/third_party/googleapis/ \
          -proto ~/cloudapi/yandex/cloud/mdb/clickhouse/v1/cluster_service.proto \
          -rpc-header "Authorization: Bearer $IAM_TOKEN" \
          -d '{
                  "cluster_id": "<идентификатор_кластера>"
              }' \
          {{ api-host-mdb }}:443 \
          yandex.cloud.mdb.clickhouse.v1.ClusterService.ListOperations
      ```

      Идентификатор кластера можно получить со [списком кластеров в каталоге](#list-clusters).

  1. Убедитесь, что запрос был выполнен успешно, изучив [ответ сервера](../api-ref/grpc/cluster_service.md#ListClusterOperationsResponse).

{% endlist %}

### Получить подробную информацию об операции {#get-operations-info}

1. [Получите список операций](#get-operations) для кластера.
1. Скопируйте идентификатор нужной операции.
1. Получите подробную информацию об операции:

    {% list tabs group=instructions %}

    - CLI {#cli}

      {% include [cli-install](../../_includes/cli-install.md) %}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      Выполните команду:

      ```bash
      yc operation get <идентификатор_операции>
      ```

      Результат:

      ```text
      id: c5m6of9he7op********
      description: Create ClickHouse cluster
      created_at: "2023-11-13T19:25:21.762164Z"
      created_by: ajeef73j5iq9********
      modified_at: "2023-11-13T19:32:00.464591Z"
      done: true
      metadata:
        '@type': type.googleapis.com/yandex.cloud.mdb.clickhouse.v1.CreateClusterMetadata
        cluster_id: c9q7vuuoiqml********
      response:
        '@type': type.googleapis.com/yandex.cloud.mdb.clickhouse.v1.Cluster
      ...
      ```

    - API {#api}

      Воспользуйтесь методом REST API [get](../api-ref/Cluster/get.md) для ресурса [Operation](../api-ref/Operation/index.md) или вызовом gRPC API [OperationService/Get](../api-ref/grpc/operation_service.md#Get) и передайте в запросе идентификатор операции.

    {% endlist %}

### См. также {#see-also}

* [{#T}](../../api-design-guide/concepts/about-async.md)

{% include [clickhouse-disclaimer](../../_includes/clickhouse-disclaimer.md) %}
