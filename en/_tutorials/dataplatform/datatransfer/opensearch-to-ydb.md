# Migrating data from {{ mos-full-name }} to {{ ydb-full-name }} using {{ data-transfer-full-name }}


With {{ data-transfer-name }}, you can transfer data from a {{ mos-name }} cluster to a {{ ydb-name }} DB.

To transfer data:

1. [Prepare the test data](#prepare-data).
1. [Prepare and activate the transfer](#prepare-transfer).
1. [Test the transfer](#verify-transfer).

If you no longer need the resources you created, [delete them](#clear-out).

## Getting started {#before-you-begin}

Prepare the infrastructure:

{% list tabs group=instructions %}

* Manually {#manual}

   1. [Create a {{ mos-name }} cluster](../../../managed-opensearch/operations/cluster-create.md) in any suitable configuration with publicly available hosts.

   1. If using security groups in your cluster, make sure they are configured correctly and allow connecting to the [{{ mos-name }}](../../../managed-opensearch/operations/connect#configuring-security-groups) cluster.

   1. [Get an SSL certificate](../../../managed-opensearch/operations/connect.md#ssl-certificate) to connect to the {{ mos-name }} cluster.

   1. [Create a {{ ydb-name }} database](../../../ydb/operations/manage-databases.md) named `ydb1` in any suitable configuration.

   1. [Create a service account](../../../iam/operations/sa/create.md#create-sa) named `ydb-account` with the `ydb.editor` role. The transfer will use it to access the database.

* Using {{ TF }} {#tf}

   1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
   1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
   1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
   1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}

   1. Download the [opensearch-to-ydb.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-opensearch-to-ydb/blob/main/opensearch-to-ydb.tf) configuration file to the same working directory.

      This file describes:

      * [Network](../../../vpc/concepts/network.md#network)..
      * [Subnet](../../../vpc/concepts/network.md#subnet)..
      * [Security group](../../../vpc/concepts/security-groups.md) and rules required to connect to the {{ mos-name }} cluster from the internet.
      * {{ ydb-name }} database.
      * {{ mos-name }} target cluster.
      * Target endpoint.
      * Transfer.

   1. In the `opensearch-to-ydb.tf` file, specify the following parameters:

      * `mos_version`: {{ OS }} version.
      * `mos_password`: User password of the {{ OS }} database owner.
      * `profile_name`: Your YC CLI profile name.

   1. Make sure the {{ TF }} configuration files are correct using this command:

      ```bash
      terraform validate
      ```

      If there are any errors in the configuration files, {{ TF }} will point them out.

   1. Create the required infrastructure:

      {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

      {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Prepare the test data {#prepare-data}

1. [Connect to the {{ mos-name }} source cluster](../../../managed-opensearch/operations/connect.md).

1. Create a test index named `people` and set its schema:

   ```bash
   curl --user admin:<password> \
        --cacert ~/.opensearch/root.crt \
        --header 'Content-Type: application/json' \
        --request PUT 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people' && \
   curl --user admin:<password> \
        --cacert ~/.opensearch/root.crt \
        --header 'Content-Type: application/json' \
        --request PUT 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_mapping?pretty' -d'
        {
              "properties": {
                 "name": {"type": "text"},
                 "age": {"type": "integer"}
              }
        }
        '
   ```

1. Populate the test index with data:

   ```bash
   curl --user admin:<password> \
        --cacert ~/.opensearch/root.crt \
        --header 'Content-Type: application/json' \
        --request POST 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_doc/?pretty' -d'
        {
              "name" : "Alice",
              "age" : "30"
        }
        ' && \
   curl --user admin:<password> \
        --cacert ~/.opensearch/root.crt \
        --header 'Content-Type: application/json' \
        --request POST 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_doc/?pretty' -d'
        {
              "name" : "Robert",
              "age" : "32"
        }
        '
   ```

1. (Optional) Check the data in the test index:

   ```bash
   curl --user admin:<password> \
        --cacert ~/.opensearch/root.crt \
        --header 'Content-Type: application/json' \
        --request GET 'https://<{{ OS }}_DATA_host_address>:{{ port-mos }}/people/_search?pretty'
   ```

## Prepare and activate the transfer {#prepare-transfer}

1. [Create a source endpoint](../../../data-transfer/operations/endpoint/source/opensearch.md#endpoint-settings) of the `{{ OS }}` type with the following settings:

   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.connection_type.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnectionType.mdb_cluster_id.title }}`.
   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnectionType.mdb_cluster_id.title }}**: Select the {{ mos-name }} cluster from the list.
   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.user.title }}**: `admin`.
   * **{{ ui-key.yc-data-transfer.data-transfer.console.form.opensearch.console.form.opensearch.OpenSearchConnection.password.title }}**: `<user_password>`.

1. Create a target endpoint and a transfer:

   {% list tabs group=instructions %}

   * Manually {#manual}

      1. [Create a target endpoint](../../../data-transfer/operations/endpoint/target/yandex-database.md#endpoint-settings) of the `{{ ydb-short-name }}` type with the following settings:

         * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbConnectionSettings.database.title }}**: Select the `ydb1` database from the list.
         * **{{ ui-key.yc-data-transfer.data-transfer.console.form.ydb.console.form.ydb.YdbConnectionSettings.service_account_id.title }}**: Select the `ydb-account` service account from the list.

      1. [Create a transfer](../../../data-transfer/operations/transfer.md#create) of the **_{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.snapshot.title }}_** type that will use the created endpoints.

      1. [Activate the transfer](../../../data-transfer/operations/transfer.md#activate) and wait for its status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-DONE }}**.

   * Using {{ TF }} {#tf}

      1. In the `opensearch-to-ydb.tf` file, specify the values for these parameters:

         * `source_endpoint_id`: Source endpoint ID.
         * `transfer_enabled`: Set to `1` for creating a target endpoint and transfer.

      1. Make sure the {{ TF }} configuration files are correct using this command:

         ```bash
         terraform validate
         ```

         If there are any errors in the configuration files, {{ TF }} will point them out.

      1. Create the required infrastructure:

         {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

      1. The transfer will be activated automatically. Wait for its status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-DONE }}**.

   {% endlist %}

## Test the transfer {#verify-transfer}

1. [Connect to the {{ ydb-name }} database](../../../ydb/operations/connection.md).

1. Run the following query:

   ```sql
   SELECT * FROM people;
   ```

   {% cut "Response example" %}

   ```text
   #|          _id           | age | name
   --+------------------------+-----+---------
   0 | "5wn5BJEBRVOYnL8d13sP" | 30  | "Alice"
   1 | "6An5BJEBRVOYnL8d13uy" | 32  | "Robert"
   ```

   {% endcut %}

## Delete the resources you created {#clear-out}

{% note info %}

Before deleting the created resources, [deactivate the transfer](../../../data-transfer/operations/transfer.md#deactivate).

{% endnote %}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

1. Delete the resources depending on how they were created:

   {% list tabs group=instructions %}

   * Manually {#manual}

      1. [Delete the transfer](../../../data-transfer/operations/transfer.md#delete).
      1. [Delete the target endpoint](../../../data-transfer/operations/endpoint/index.md#delete).
      1. [Delete the {{ mos-name }} cluster](../../../managed-opensearch/operations/cluster-delete.md).
      1. [Delete the {{ ydb-name }} database](../../../ydb/operations/manage-databases.md#delete-db).
      1. [Delete the service account](../../../iam/operations/sa/delete.md).

   * Using {{ TF }} {#tf}

      1. In the terminal window, go to the directory containing the infrastructure plan.
      1. Delete the `opensearch-to-ydb.tf` configuration file.
      1. Make sure the {{ TF }} configuration files are correct using this command:

         ```bash
         terraform validate
         ```

         If there are any errors in the configuration files, {{ TF }} will point them out.

      1. Confirm updating the resources.

         {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

         This will delete all the resources described in the `opensearch-to-ydb.tf` configuration file.

   {% endlist %}

1. [Delete the source endpoint](../../../data-transfer/operations/endpoint/index.md#delete).
