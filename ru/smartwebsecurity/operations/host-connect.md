---
title: "Как подключить профиль безопасности {{ sws-full-name }} к виртуальному хосту"
description: "Следуя данной инструкции, вы сможете подключить профиль безопасности {{ sws-full-name }} к виртуальному хосту."
---

# Подключить профиль безопасности к виртуальному хосту

Способ подключения профиля безопасности зависит от того, кто управляет балансировщиком [{{ alb-full-name }}](../../application-load-balancer/concepts/index.md):

* Если балансировщик управляется вами, то используйте интерфейсы {{ yandex-cloud }}.

* Если балансировщик управляется [Ingress-контроллером](../../application-load-balancer/tools/k8s-ingress-controller/index.md) {{ alb-name }}, используйте [аннотацию ресурса Ingress](../../application-load-balancer/k8s-ref/ingress.md#annot-security-profile-id).

    {% note warning %}

    Аннотация — единственный способ подключения профиля безопасности для Ingress-контроллера.

    Если вы подключите профиль через интерфейсы {{ yandex-cloud }} и затем обновите ресурс Ingress, из-за отсутствия аннотации Ingress-контроллер отключит поддержку профиля безопасности.

    {% endnote %}

    О настройке Ingress-контроллера читайте в [документации {{ managed-k8s-full-name }}](../../managed-kubernetes/tutorials/alb-ingress-controller.md).

Чтобы подключить профиль безопасности с помощью интерфейсов {{ yandex-cloud }}:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder), в котором находится нужный [профиль безопасности](../concepts/profiles.md).
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_smartwebsecurity }}**.
  1. Выберите профиль безопасности, который вы хотите подключить к [виртуальному хосту](../../application-load-balancer/concepts/http-router.md#virtual-host) сервиса [{{ alb-full-name }}](../../application-load-balancer/).
  1. Нажмите кнопку ![plug](../../_assets/console-icons/plug-connection.svg) **{{ ui-key.yacloud.smart-web-security.overview.action_attach-to-host }}** и в открывшемся окне выберите:
      * [**{{ ui-key.yacloud.smart-web-security.attach-dialog.label_balancer }}**](../../application-load-balancer/concepts/application-load-balancer.md).
      * [**{{ ui-key.yacloud.smart-web-security.attach-dialog.label_http-router }}**](../../application-load-balancer/concepts/http-router.md).
      * **{{ ui-key.yacloud.smart-web-security.attach-dialog.label_virtual-host }}**. Вы можете подключить профиль безопасности сразу к нескольким хостам.

        Чтобы подключить профиль к еще одному L7-балансировщику, нажмите кнопку **{{ ui-key.yacloud.smart-web-security.attach-dialog.action_add-balancer }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.smart-web-security.attach-dialog.action_connect }}**. Если выбранные хосты уже подключены к другому профилю безопасности, подтвердите подключение.

      На вкладке **{{ ui-key.yacloud.smart-web-security.overview.title_connected-to-the-hosts }}** появятся подключенные виртуальные хосты.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. Чтобы посмотреть список имеющихся профилей безопасности в каталоге по умолчанию, выполните команду:

     {% include [security-profile-list-command](../../_includes/smartwebsecurity/security-profile-list-command.md) %}

  1. Чтобы посмотреть список [HTTP-роутеров](../../application-load-balancer/concepts/http-router.md) в каталоге по умолчанию, выполните команду:

     ```bash
     yc application-load-balancer http-router list
     ```

     Результат:

     ```text
     +----------------------+-------------------+-------------+-------------+
     |          ID          |       NAME        | VHOST COUNT | ROUTE COUNT |
     +----------------------+-------------------+-------------+-------------+
     | ds7e9te73uak******** |  my-first-router  |           1 |           1 |
     +----------------------+-------------------+-------------+-------------+
     ```

  1. Чтобы посмотреть список [виртуальных хостов](../../application-load-balancer/concepts/http-router.md#virtual-host) выбранного HHTP-роутера, выполните команду:

     ```bash
     yc application-load-balancer http-router get <имя_или_идентификатор_HTTP-роутера>
     ```

     Результат:

     ```text
     id: ds7e9te73uak********
     name: my-first-router
     folder_id: b1gt6g8ht345********
     virtual_hosts:
       - name: test-virtual-host
         routes:
           - name: test-route
             http:
               match:
                 path:
                   prefix_match: /
               route:
                 backend_group_id: ds7a4niks9qv********
                 timeout: 60s
                 auto_host_rewrite: false
         route_options: {}
     created_at: "2024-08-05T08:34:03.973000654Z"
     ```

     Имена виртуальных хостов указаны в параметре `virtual_hosts.name`. В примере выше только один виртуальный хост — `test-virtual-host`.

  1. Чтобы подключить [профиль безопасности](../concepts/profiles.md) к виртуальному хосту, выполните команду:

     ```bash
     yc application-load-balancer virtual-host update <имя_виртуального_хоста> \
        --http-router-name <имя_HTTP-роутера> \
        --security-profile-id <идентификатор_профиля_безопасности>
     ```

     Где:

     * `<имя_виртуального_хоста>` — имя виртуального хоста, полученное на предыдущем шаге.
     * `--http-router-name` — имя HTTP-роутера. Обязательный параметр. Вместо имени HTTP-роутера вы можете передать его идентификатор в параметре `--http-router-id`.
     * `--security-profile-id` — идентификатор профиля безопасности. Обязательный параметр.

     Результат:

     ```text
     done (1s)
     name: test-virtual-host
     routes:
       - name: test-route
         http:
           match:
             path:
               prefix_match: /
           route:
             backend_group_id: ds7a4niks9qv********
             timeout: 60s
             auto_host_rewrite: false
     route_options:
       security_profile_id: fev3s055oq64********
      ```

  Подробнее о команде `yc application-load-balancer virtual-host update` читайте в [справочнике CLI](../../cli/cli-ref/managed-services/application-load-balancer/virtual-host/update.md).

- API {#api}

  {% include [api-host](../../_includes/smartwebsecurity/api-host.md) %}

{% endlist %}

{% include [auto-scaling-tip](../../_includes/smartwebsecurity/auto-scaling-tip.md) %}

## См. также {#see-also}

* [{#T}](host-delete.md)
