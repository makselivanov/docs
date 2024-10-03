{% list tabs %}

- {{ mch-name }}

    1. [Create a target database](../../../../managed-clickhouse/operations/databases.md#add-db).

       If you need to transfer multiple databases, create a separate transfer for each one of them.

    1. [Create a user](../../../../managed-clickhouse/operations/cluster-users.md#adduser) with access to the target database.

       Once started, the transfer will connect to the target on behalf of this user.

    1. If [user management via SQL](../../../../managed-clickhouse/operations/cluster-users.md#sql-user-management) is enabled in the cluster, grant the new user the following permissions:

        ```sql
        GRANT CLUSTER ON *.* TO <username>
        GRANT SELECT, INSERT, ALTER DELETE, CREATE TABLE, CREATE VIEW, DROP TABLE, TRUNCATE, dictGet ON <DB_name>.* TO <username>
        GRANT SELECT(macro, substitution) ON system.macros TO <username>
        ``` 

       If user management via SQL is disabled, [permissions are assigned](../../../../managed-clickhouse/operations/cluster-users.md) via the management console and CLI.

    1. [Create a security group](../../../../vpc/operations/security-group-create.md) and [configure it](../../../../managed-clickhouse/operations/connect/index.md#configuring-security-groups).

    1. Assign the created security group to the {{ mch-name }} cluster.

- {{ CH }}

    1. {% include notitle [White IP list](../../configure-white-ip.md) %}

    1. Create a target database. Its name must be the same as the source database name. If you need to transfer multiple databases, create a separate transfer for each one of them.

    1. Create a user with access to the target database.

       Once started, the transfer will connect to the target on behalf of this user.

    1. Grant the new user the following permissions:

        ```sql
        GRANT CLUSTER ON *.* TO <username>
        GRANT SELECT, INSERT, ALTER DELETE, CREATE TABLE, CREATE VIEW, DROP TABLE, TRUNCATE, dictGet ON <DB_name>.* TO <username>
        GRANT SELECT(macro, substitution) ON system.macros TO <username>
        ```

{% endlist %}