---
title: "How to connect to a {{ k8s }} cluster node via {{ oslogin }} in {{ managed-k8s-full-name }}"
description: "Follow this guide to connect to a node via {{ oslogin }}."
---

# Connecting to a node via {{ oslogin }}

[{{ oslogin }}](../../organization/concepts/os-login.md) is used instead of SSH keys to access {{ yandex-cloud }} virtual machines via SSH. With {{ oslogin }}, you can connect to {{ managed-k8s-name }} nodes.

{% include [node-vm-explained](../../_includes/managed-kubernetes/node-vm-explained.md) %}

{% include [node-vm-manipulation-warning](../../_includes/managed-kubernetes/node-vm-manipulation-warning.md) %}

[Configure your cluster node](#configure-node) and then connect to it using one of the two methods:

* [Using the CLI](#connect-via-cli).
* [Using the SSH](#connect-via-ssh).

## Getting started

1. {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

1. [Enable access via {{ oslogin }}](../../organization/operations/os-login-access.md) at the organization level.

1. [Enable access to nodes from the internet](./node-group/node-group-update.md#node-internet-access) for the node group containing the node you need to connect to.

1. Make sure the account you are using to connect to the node [has one of these roles](../../iam/operations/roles/grant.md) assigned:

    * `compute.osLogin`: To access the node without sudo permissions.
    * `compute.osAdminLogin`: To access the node with sudo permissions.

## Configure the node {#configure-node}

Set up your cluster node for connection:

1. Make sure to enable [external access](./node-group/node-group-update.md#node-internet-access) for the node.

1. Activate node access via {{ oslogin }} by changing the method of connecting to nodes.

    {% include [node-connect-mode-reconciling-warning](../../_includes/managed-kubernetes/node-connect-mode-reconciling-warning.md) %}

    {% list tabs group=instructions %}

    - Management console {#console}

        1. Open the **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kubernetes }}** section in the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) containing the {{ managed-k8s-name }} cluster whose node you need access to.
        1. Click the name of the {{ managed-k8s-name }} cluster.
        1. Go to the **{{ ui-key.yacloud.k8s.nodes.label_node-groups }}** tab.
        1. Select the required node group.
        1. Click **{{ ui-key.yacloud.common.edit }}** in the top-right corner.
        1. Select **{{ ui-key.yacloud.compute.instances.create.field_os-login-access-method }}**.

            {% include [note-oslogin-ssh-warning](../../_includes/managed-kubernetes/note-oslogin-ssh-warning.md) %}

        1. Click **{{ ui-key.yacloud.common.save }}**.

    - CLI {#cli}

      {% include [cli-install](../../_includes/cli-install.md) %}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      To enable {{ oslogin }} for all nodes in a node group:

      1. View the description of the CLI command for adding and updating the {{ managed-k8s-name }} node group metadata:

          ```bash
          {{ yc-k8s }} node-group add-metadata --help
          ```

      1. Run this command:

          ```bash
          {{ yc-k8s }} node-group add-metadata \
            --name <node_group_name> \
            --metadata enable-oslogin=true
          ```

          You can request the name of a node group with a [list of node groups in the folder](./node-group/node-group-list.md#list).

          {% include [note-oslogin-ssh-warning](../../_includes/managed-kubernetes/note-oslogin-ssh-warning.md) %}

    - {{ TF }} {#tf}

      1. Open the current {{ TF }} configuration file describing the {{ managed-k8s-name }} node group.

          For more information about creating this file, see [{#T}](./node-group/node-group-create.md).

      1. Add the `instance_template.metadata` parameter to the node group description, or change it if it already exists.

          In this parameter, specify the `enable-oslogin` metadata key with the `true` value:

          ```hcl
          resource "yandex_kubernetes_node_group" "<node_group_name>" {
            cluster_id = yandex_kubernetes_cluster.<cluster_name>.id
            ...
            instance_template {
              metadata = {
                "enable-oslogin" = "true"
                ...
              }
              ...
            }
            ...
          }
          ```

          {% include [note-oslogin-ssh-warning](../../_includes/managed-kubernetes/note-oslogin-ssh-warning.md) %}

      1. Make sure the configuration files are correct.

          {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Confirm updating the resources.

          {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      For more information, see the [{{ TF }}]({{ tf-provider-k8s-nodegroup }}) provider documentation.

    - API {#api}

      1. {% include [get-metadata-via-api](../../_includes/managed-kubernetes/get-metadata-via-api.md) %}

      1. Use the [update](../api-ref/NodeGroup/update.md) API method and include the following in the request:

          * Node group ID in the `nodeGroupId` parameter.

          * The `updateMask` parameter set to `nodeTemplate.metadata`.

            {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

          * `nodeTemplate.metadata` parameter listing all existing node group metadata as `key=value` pairs without modification.

            For the `enable-oslogin` key, replace the current value with `true`. If there is no such key, add it.

            {% include [note-oslogin-ssh-warning](../../_includes/managed-kubernetes/note-oslogin-ssh-warning.md) %}

            {% cut "Example of listing metadata in a parameter" %}

            > * Existing metadata keys in a node group:
            >
            >   ```json
            >   "nodeTemplate": {
            >       "metadata": {
            >           "enable-oslogin": "undefined",
            >           "<existing_key_1>": "<existing_value_1>",
            >           "<existing_key_2>": "<existing_value_2>"
            >       },
            >       ...
            >   }
            >   ```
            >
            > * Metadata keys to provide in an API request:
            >
            >   ```json
            >   "nodeTemplate": {
            >       "metadata": {
            >           "enable-oslogin": "true",
            >           "<existing_key_1>": "<existing_value_1>",
            >           "<existing_key_2>": "<existing_value_2>"
            >       }
            >   }
            >   ```

            {% endcut %}

            {% include [Alert API Updating Metadata](../../_includes/managed-kubernetes/metadata-updating-alert.md) %}

    {% endlist %}

## Connect to the node using the CLI {#connect-via-cli}

1. View the description of the CLI command for connection to the node:

    ```bash
    yc compute ssh --help
    ```

1. To find out the name of the node you need, get a list of cluster nodes.

    ```bash
    {{ yc-k8s }} node-group list-nodes --name <node_group_name>
    ```

    Result example:

    ```bash
    +----------------------+-----------------+---------------------------+-------------+--------+
    | CLOUD INSTANCE       | KUBERNETES NODE | RESOURCES                 | DISK        | STATUS |
    +----------------------+-----------------+---------------------------+-------------+--------+
    | fhmmh23ugigb******** | <node_name>      | 4 100% core(s), 8.0 GB of | 64.0 GB ssd | READY  |
    | RUNNING_ACTUAL       |                 | memory                    |             |        |
    +----------------------+-----------------+---------------------------+-------------+--------+
    ```

1. Connect to the node:

    ```bash
    yc compute ssh --name <node_name>
    ```

## Connect to the node using the SSH {#connect-via-ssh}

1. [Export the {{ oslogin }} certificate](../../compute/operations/vm-connect/os-login-export-certificate.md).

   {% note info %}

   The certificate is valid for one hour. After this time has elapsed, you will need to export a new certificate to connect to the node.

   {% endnote %}

1. Find out the public address of the node:

   1. Get the node group ID:

      ```bash
      {{ yc-k8s }} node-group list
      ```

      Result:

      ```text
      +------------------------------+----------------------+-----------+----------------------+---------------------+---------+------+
      |          ID                  |      CLUSTER ID      |   NAME    |  INSTANCE GROUP ID   |     CREATED AT      | STATUS  | SIZE |
      +------------------------------+----------------------+-----------+----------------------+---------------------+---------+------+
      | <node_group_ID> | cato4gqs0ave******** | ng-name   | cl17a1c3mbau******** | 2024-02-08 04:25:06 | RUNNING |    1 |
      +------------------------------+----------------------+-----------+----------------------+---------------------+---------+------+
      ```

      You will find the parameter you need in the `ID` column.

   1. View the list of {{ managed-k8s-name }} nodes that belong to this group:

      ```bash
      yc compute instance-group list-instances <node_group_ID>
      ```

      Result:

      ```text
      +----------------------+---------------------------+----------------+-------------+----------------------+----------------+
      |     INSTANCE ID      |           NAME            |  EXTERNAL IP   | INTERNAL IP |        STATUS        | STATUS MESSAGE |
      +----------------------+---------------------------+----------------+-------------+----------------------+----------------+
      | fhm8nq5p7t0r******** | cl12kvrgj493rhrkimmb-**** | 84.201.156.211 | 10.128.0.36 | RUNNING_ACTUAL [25m] |                |
      +----------------------+---------------------------+----------------+-------------+----------------------+----------------+
      ```

      The public IP address of the {{ managed-k8s-name }} node is listed in the `EXTERNAL IP` column.

1. Connect to the VM:

    ```bash
    ssh -i <certificate_file_path> <username>@<node_public_IP_address>
    ```

    Where:

    * `<certificate_file_path>`: Path to the previously saved `Identity` file of the certificate. For example: `/home/user1/.ssh/yc-cloud-id-b1gia87mbaom********-orgusername`.
    * `<username>`: Organization user's name. It is specified at the end of the exported {{ oslogin }} certificate's name. In the example above, it is `orgusername`.
    * `<node_public_IP_address>`: Public IP address of the node obtained earlier.

    If this is your first time connecting to the node, you will get an unknown host warning:

    ```text
    The authenticity of host '158.160.**.** (158.160.**.**)' can't be established.
    ECDSA key fingerprint is SHA256:PoaSwqxRc8g6iOXtiH7ayGHpSN0MXwUfWHk********.
    Are you sure you want to continue connecting (yes/no)?
    ```

    Type `yes` in the terminal and press **Enter**.
