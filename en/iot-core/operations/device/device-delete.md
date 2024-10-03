# Deleting a device

To access a [device](../../concepts/index.md#device), use its unique ID or name. For information about how to get its unique ID or name, see [{#T}](device-list.md)

{% list tabs group=instructions %}

- Management console {#console}

   To delete a device:

   1. In the [management console]({{ link-console-main }}), select the folder to delete the device from.
   1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_iot-core }}**.
   1. Select the required registry from the list.
   1. On the left side of the window, select the **{{ ui-key.yacloud.iot.label_devices }}** section.
   1. To the right of the device name, click ![image](../../../_assets/console-icons/ellipsis.svg)and select **{{ ui-key.yacloud.common.delete }}** from the drop-down list.
   1. In the window that opens, click **{{ ui-key.yacloud.common.delete }}**.

- CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  1. Delete the device:

      ```bash
      yc iot device delete my-device
      ```

  1. Make sure the device was deleted:

      ```bash
      yc iot device list --registry-name my-registry
	    ```

	  Result:
	  ```text
      +----+------+
      | ID | NAME |
      +----+------+
      +----+------+
      ```

- {{ TF }} {#tf}

  {% include [terraform-definition](../../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  To delete a device created using {{ TF }}:
  
  1. Open the {{ TF }} configuration file and delete the fragment with the device description.

      Example device description in the {{ TF }} configuration:

      ```hcl
      resource "yandex_iot_core_device" "my_device" {
        registry_id = "<registry_ID>"
        name        = "test-device"
        description = "test device for terraform provider documentation"
      ...
      }
      ```

      For more information about the `yandex_iot_core_device` parameters in {{ TF }}, see the [provider documentation]({{ tf-provider-resources-link }}/iot_core_device).
  1. In the command line, change to the folder where you edited the configuration file.
  1. Make sure the configuration file is correct using this command:

      ```bash
      terraform validate
      ```

      If the configuration is correct, you will get this message:
     
      ```bash
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

  1. Confirm the changes: type `yes` into the terminal and click **Enter**.

      You can check the update using the [management console]({{ link-console-main }}) or this [CLI](../../../cli/quickstart.md) command:

      ```bash
      yc iot device list --registry-id <registry_ID>
      ```

- API {#api}

  To delete a device, use the [delete](../../api-ref/Device/delete.md) REST API method for the [Device](../../api-ref/Device/index.md) resource or the [DeviceService/Delete](../../api-ref/grpc/device_service.md#Delete) gRPC API call.

{% endlist %}