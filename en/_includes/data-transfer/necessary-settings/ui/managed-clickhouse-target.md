* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseManaged.mdb_cluster_id.title }}**: From the list, select the name of the cluster you want to connect to.

* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.shard_group.title }}**: Specify the shard group to transfer the data to. If this value is not set, the data will go to all shards.

* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.user.title }}**: Specify the username that {{ data-transfer-name }} will use to connect to the database.

* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.password.title }}**: Enter the user's password to the database.

* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.database.title }}**: Specify the name of the database in the selected cluster.

* **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.security_groups.title }}**: Select the cloud network to host the endpoint and security groups for network traffic. This will allow you to apply the specified security group rules to the VMs and clusters in the selected network without changing their settings. For more information, see [{#T}](../../../../data-transfer/concepts/network.md).

    Make sure the selected security groups are [configured](../../../../managed-clickhouse/operations/connect/index.md#configuring-security-groups).

