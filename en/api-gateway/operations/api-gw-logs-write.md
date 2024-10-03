---
title: "Writing to the execution log in {{ api-gw-full-name }}"
description: "Follow this guide to configure API gateway logging."
---

# Writing to the execution log in {{ api-gw-name }}

{% include [logging-note](../../_includes/functions/logging-note.md) %}

{% list tabs group=instructions %}

- Management console {#console}
    
    1. In the [management console]({{ link-console-main }}), go the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) containing the API gateway.
    1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_api-gateway }}**.
    1. Select the API gateway you want to configure logging for.
    1. At the top of the page, click ![image](../../_assets/console-icons/pencil.svg) **{{ ui-key.yacloud.serverless-functions.gateways.overview.button_action-edit }}**.
    1. Under **{{ ui-key.yacloud.logging.label_title }}**, select the following in the **{{ ui-key.yacloud.logging.label_destination }}** field:
        * `{{ ui-key.yacloud.serverless-functions.item.editor.option_queues-unset }}`: To disable logging.
        * `{{ ui-key.yacloud.common.folder }}`: To write logs to the default [log group](../../logging/concepts/log-group.md) for the folder containing the API gateway.
            1. (Optional) In the **{{ ui-key.yacloud.logging.label_minlevel }}** field, select the minimum logging level.
        * `{{ ui-key.yacloud.logging.label_loggroup }}`: To write logs to a custom log group.
            1. (Optional) In the **{{ ui-key.yacloud.logging.label_minlevel }}** field, select the minimum logging level.
            1. In the **{{ ui-key.yacloud.logging.label_loggroup }}** field, select the log group to write the logs to. If you do not have a log group, [create one](../../logging/operations/create-group.md).
    1. Click **{{ ui-key.yacloud.serverless-functions.gateways.form.button_update-gateway }}**. 
    
    {% include [min-log-level](../../_includes/api-gateway/min-log-level.md) %}

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    ## Logging destination {#destination}

    {% include [logging-destination](../../_includes/api-gateway/logging-destination.md) %}

    To write logs to a custom log group, provide the log group ID in the `--log-group-id` parameter when [creating](api-gw-create.md) or [modifying](api-gw-update.md) an API gateway. The log group must reside in the same folder as the API gateway.

    ## Minimum logging level {#log-level}

    To set a minimum logging level, provide it in the `--min-log-level` parameter when creating or modifying an API gateway. 

    {% include [min-log-level](../../_includes/api-gateway/min-log-level.md) %}

    ## Disabling logging {#disabled}

    To disable logging, set the `--no-logging` parameter when creating or modifying an API gateway.

    ## Command example {#example}

    To write logs to a custom log group, run this command:

    ```bash
    {{ yc-serverless }} api-gateway update <API_gateway_name_or_ID> \
      --log-group-id <log_group_ID> \
      --min-log-level <minimum_logging_level>
    ```

    Where:
    * `--log-group-id`: ID of the log group to write logs to.
    * `--min-log-level`: Minimum logging level. The available levels are `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, and `FATAL`. This is an optional parameter.

    Result:

    ```text
    id: d5dr8k465604********
    folder_id: b1g3f9i71bpm********
    created_at: "2024-01-26T09:18:55.985Z"
    name: example_gateway
    status: ACTIVE
    domain: d5dr8k465604********.apigw.yandexcloud.net
    log_group_id: ckgsh1kdbvj1********
    connectivity: {}
    log_options:
      log_group_id: e23u2vn449av********
      min_level: ERROR
    ```

- {{ TF }} {#tf}
    
    {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}
    
    {% include [terraform-install](../../_includes/terraform-install.md) %}

    ## Logging destination {#destination}

    {% include [logging-destination](../../_includes/api-gateway/logging-destination.md) %}

    To write logs to a custom log group, under `log_options`, provide the log group ID in the `log_group_id` parameter when [creating](api-gw-create.md) or [modifying](api-gw-update.md) an API gateway. The log group must reside in the same folder as the API gateway.

    ## Minimum logging level {#log-level}

    To set a minimum logging level, provide it in the `log_group_id` parameter under `log_options` when creating or modifying an API gateway. 

    {% include [min-log-level](../../_includes/api-gateway/min-log-level.md) %}

    ## Disabling logging {#disabled}

    To disable logging, under `log_options`, set the `disabled` parameter to `true` when creating or modifying an API gateway.

    ## Example {#example}

    To write logs to a custom log group:

    1. Open the {{ TF }} configuration file and add the `log_options` section to the `yandex_api_gateway` resource description:

        Here is an example of the configuration file structure:
        
        ```hcl
        resource "yandex_api_gateway" "<API_gateway_name>" {
          ...
          log_options {
            folder_id = "<folder_ID>"
            min_level = "<minimum_logging_level>"
          }
          ...
        }
        ```

        Where:
        * `folder_id`: Folder ID.
        * `min_level`: Minimum logging level. The available levels are `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, and `FATAL`. This is an optional parameter.

        For more information about the `yandex_api_gateway` resource parameters in {{ TF }}, see the [relevant provider documentation]({{ tf-provider-resources-link }}/api_gateway).
    
    1. Check the configuration using this command:

        ```bash
        terraform validate
        ```

        If the configuration is correct, you will get this message:

        ```text
        Success! The configuration is valid.
        ```

    1. Run this command:

        ```bash
        terraform plan
        ```

        The terminal will display a list of resources with parameters. No changes will be made at this step. If the configuration contains any errors, {{ TF }} will point them out.
    
    1. Apply the configuration changes:

        ```bash
        terraform apply
        ```

    1. Confirm the changes: type `yes` into the terminal and press **Enter**.

- API {#api}

    To write to the execution log in {{ api-gw-full-name }}, use the [update](../apigateway/api-ref/ApiGateway/update.md) REST API method for the [ApiGateway](../apigateway/api-ref/ApiGateway/index.md) resource or the [ApiGatewayService/Update](../apigateway/api-ref/grpc/apigateway_service.md#Update) gRPC API call.

{% endlist %}
