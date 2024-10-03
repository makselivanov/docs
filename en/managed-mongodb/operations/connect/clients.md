---
title: "Connecting to a {{ MG }} cluster in {{ mmg-full-name }}"
description: "Follow this guide to connect to a database in a {{ MG }} cluster using command line tools, graphical IDEs, and Docker container."
---

# Connecting to a {{ MG }} cluster from applications

You can connect to a {{ MG }} cluster using [command line tools](#command-line-tools), [graphical IDEs](#connection-ide), and [Docker container](#connection-docker). To learn how to connect from your application code, see [Code examples](code-examples.md).

The examples below assume that the `root.crt` [SSL certificate](index.md#get-ssl-cert) is located in this directory:

* `/home/<home_directory>/.mongodb/ `for Ubuntu
* `$HOME\.mongodb` for Windows

If the connection to the cluster is successful and the test query is executed, the name of the database to which the connection was made will be displayed.

## Command line tools {#command-line-tools}

{% include [see-fqdn-in-console](../../../_includes/mdb/see-fqdn-in-console.md) %}

The setup method depends on whether cluster [sharding](../../concepts/sharding.md) is enabled:

### Linux (Bash) {#bash}

Before connecting, install the [MongoDB Shell utility]({{ shell-link }}).

{% list tabs group=connection %}

- Connecting via SSL for {{ MG }} 4.2 and higher {#with-ssl}

   For a non-sharded cluster:

   ```bash
   mongosh --norc \
           --tls \
           --tlsCAFile /home/<home_directory>/.mongodb/root.crt \
           --host '<FQDN_of_{{ MG }}_host_1>:{{ port-mmg }},...,<FQDN_of_{{ MG }}_host_N>:{{ port-mmg }}' \
           --username <DB_username> \
           --password <DB_user_password> \
           <DB_name>
   ```

   For a sharded cluster:

   ```bash
   mongosh --norc \
           --tls \
           --tlsCAFile /home/<home_directory>/.mongodb/root.crt \
           --host '<FQDN_of_MONGOINFRA_or_MONGOS_host_1>:27017,...,<FQDN_of_MONGOINFRA_or_MONGOS_host_N>:27017' \
           --username <DB_username> \
           --password <DB_user_password> \
           <DB_name>
   ```

- Connecting via SSL for {{ MG }} 4.0 {#with-ssl-4}

   For a non-sharded cluster:

   ```bash
   mongosh --norc \
           --ssl \
           --sslCAFile /home/<home_directory>/.mongodb/root.crt \
           --host '<FQDN_of_{{ MG }}_host_1>:{{ port-mmg }},...,<FQDN_of_{{ MG }}_host_N>:{{ port-mmg }}' \
           --username <DB_username> \
           --password <DB_user_password> \
           <DB_name>
   ```

   For a sharded cluster:

   ```bash
   mongosh --norc \
           --ssl \
           --sslCAFile /home/<home_directory>/.mongodb/root.crt \
           --host '<FQDN_of_MONGOINFRA_or_MONGOS_host_1>:27017,...,<FQDN_of_MONGOINFRA_or_MONGOS_host_N>:27017' \
           --username <DB_username> \
           --password <DB_user_password> \
           <DB_name>
   ```

- Connecting without SSL {#without-ssl}

   For a non-sharded cluster:

   ```bash
   mongosh --norc \
           --host '<FQDN_of_{{ MG }}_host_1>:{{ port-mmg }},...,<FQDN_of_{{ MG }}_host_N>:{{ port-mmg }}' \
           --username <DB_username> \
           --password <DB_user_password> \
           <DB_name>
   ```

   For a sharded cluster:

   ```bash
   mongosh --norc \
           --host '<FQDN_of_MONGOINFRA_or_MONGOS_host_1>:27017,...,<FQDN_of_MONGOINFRA_or_MONGOS_host_N>:27017' \
           --username <DB_username> \
           --password <DB_user_password> \
           <DB_name>
   ```

{% endlist %}

{% include [see-fqdn-host](../../../_includes/mdb/mmg/fqdn-host.md) %}

After connecting, run the `db` command.

### Windows (PowerShell) {#powershell}

Before connecting, install the [MongoDB Shell utility](https://www.mongodb.com/try/download/shell).

{% list tabs group=connection %}

- Connecting via SSL for {{ MG }} 4.2 and higher {#with-ssl}

   For a non-sharded cluster:

   ```powershell
   mongosh.exe --norc `
               --host '<FQDN_of_{{ MG }}_host_1>:{{ port-mmg }},...,<FQDN_of_{{ MG }}_host_N>:{{ port-mmg }}' `
               --tls `
               --tlsCAFile $HOME\.mongodb\root.crt `
               --username <DB_username> `
               --password <DB_user_password> `
               <DB_name>
   ```

   For a sharded cluster:

   ```powershell
   mongosh.exe --norc `
               --host '<FQDN_of_MONGOINFRA_or_MONGOS_host_1>:{{ port-mmg-sharded }},...,<FQDN_of_MONGOINFRA_or_MONGOS_host_N>:{{ port-mmg-sharded }}' `
               --tls `
               --tlsCAFile $HOME\.mongodb\root.crt `
               --username <DB_username> `
               --password <DB_user_password> `
               <DB_name>
   ```

- Connecting without SSL {#without-ssl}

   For a non-sharded cluster:

   ```powershell
   mongosh.exe --norc `
               --host '<FQDN_of_{{ MG }}_host_1>:{{ port-mmg }},...,<FQDN_of_{{ MG }}_host_N>:{{ port-mmg }}' `
               --username <DB_username> `
               --password <DB_user_password> `
               <DB_name>
   ```

   For a sharded cluster:

   ```powershell
   mongosh.exe --norc `
               --host '<FQDN_of_MONGOINFRA_or_MONGOS_host_1>:{{ port-mmg-sharded }},...,<FQDN_of_MONGOINFRA_or_MONGOS_host_N>:{{ port-mmg-sharded }}' `
               --username <DB_username> `
               --password <DB_user_password> `
               <DB_name>
   ```

{% endlist %}

{% include [see-fqdn-host](../../../_includes/mdb/mmg/fqdn-host.md) %}

After connecting, run the `db` command.

## Connecting from graphical IDEs {#connection-ide}

{% include [ide-environments](../../../_includes/mdb/mmg-ide-envs.md) %}

You can only use graphical IDEs to connect to public cluster hosts using an [SSL certificate](index.md#get-ssl-cert).

{% include [note-connection-ide](../../../_includes/mdb/note-connection-ide.md) %}

### DataGrip {#datagrip}

1. Create a data source:
   1. Select **File** → **New** → **Data Source** → **{{ MG }}**.
   1. On the **General** tab:
      1. Specify the connection parameters:
         * **User**, **Password**: DB user's name and password.
         * **URL**: Connection string.

            For a non-sharded cluster:

            ```http
            mongodb://<FQDN_of_{{ MG }}_host_1>:{{ port-mmg }},..,<FQDN_of_{{ MG }}_host_N>:{{ port-mmg }}/<DB_name>
            ```

            For a [sharded](../../concepts/sharding.md) cluster:

            ```http
            mongodb://<FQDN_of_MONGOINFRA_or_MONGOS_host_1>:{{ port-mmg-sharded }},...<FQDN_of_MONGOINFRA_or_MONGOS_host_N>:{{ port-mmg-sharded }}/<DB_name>
            ```

            {% include [see-fdqn-host](../../../_includes/mdb/mmg/fqdn-host.md) %}

      1. Click **Download** to download the connection driver.
   1. On the **SSH/SSL** tab:
      1. Enable the **Use SSL** setting.
      1. In the **CA file** field, specify the path to the file with an [SSL certificate for the connection](./index.md#get-ssl-cert).
1. Click **Test Connection** to test the connection. If the connection is successful, you will see the connection status and information about the DBMS and driver.
1. Click **OK** to save the data source.

### DBeaver {#dbeaver}

Connections to {{ MG }} clusters are only available in [DBeaver business editions](https://dbeaver.com/buy/).

To connect to a cluster:

1. Create a new DB connection:
   1. In the **Database** menu, select **New connection**.
   1. Select **{{ MG }}** from the DB list.
   1. Click **Next**.
   1. Specify the connection parameters on the **Main** tab:
      1. Under **Address**, change **Type** to `URL` and specify the connection string.

         For a non-sharded cluster:

         ```http
         mongodb://<FQDN_of_{{ MG }}_host_1>:{{ port-mmg }},..,<FQDN_of_{{ MG }}_host_N>:{{ port-mmg }}/<DB_name>
         ```

         For a [sharded](../../concepts/sharding.md) cluster:

         ```http
         mongodb://<FQDN_of_MONGOINFRA_or_MONGOS_host_1>:{{ port-mmg-sharded }},...<FQDN_of_MONGOINFRA_or_MONGOS_host_N>:{{ port-mmg-sharded }}/<DB_name>
         ```

         {% include [see-fdqn-host](../../../_includes/mdb/mmg/fqdn-host.md) %}

      1. In the **Device** list, select `SCRAM-SHA-256` (type of password encryption when connecting to the DB).
      1. Under **Authentication**, specify the DB username and password.
   1. On the **SSL** tab:
      1. Enable **Use SSL**.
      1. In the **Root certificate** field, specify the path to the saved [SSL certificate](./index.md#get-ssl-cert) file.
      1. Under **Settings**, select **Skip hostname validation**.
1. Click **Test connection ...** to test the connection. If the connection is successful, you will see the connection status and information about the DBMS and driver.
1. Click **Ready** to save the database connection settings.

## Before you connect from a Docker container {#connection-docker}

To connect to a {{ mmg-name }} cluster from a Docker container using SSL, add the following lines to the Dockerfile:

```bash
RUN apt-get update && \
    apt-get install wget --yes && \
    mkdir --parents ~/.mongodb && \
    wget "{{ crt-web-path }}" \
         --output-document ~/.mongodb/root.crt && \
    chmod 0644 ~/.mongodb/root.crt
```

To connect without SSL, no additional Dockerfile settings are required.

After running the Docker container, switch to it and [install](https://www.mongodb.com/docs/mongodb-shell/install/) the `mongosh` utility. You will need it to connect to the cluster.
