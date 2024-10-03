# Creating a {{ GP }} cluster

A {{ mgp-name }} cluster consists of master hosts that accept client queries and segment hosts that provide data processing and storage capability.

Available disk types [depend](../concepts/storage.md) on the selected [host class](../concepts/instance-types.md).

For more information, see [{#T}](../concepts/index.md).

## Creating a cluster {#create-cluster}

To create a {{ mgp-name }} cluster, you need the [{{ roles-vpc-user }}](../../vpc/security/index.md#vpc-user) role and the [{{ roles.mgp.editor }} role or higher](../security/index.md#roles-list). For more information on assigning roles, see the [{{ iam-name }} documentation](../../iam/operations/roles/grant.md).

{% list tabs group=instructions %}

- Management console {#console}

    To create a {{ mgp-name }} cluster:

    1. In the [management console]({{ link-console-main }}), select the folder where you want to create a database cluster.
    1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
    1. Enter a name for the cluster. It must be unique within the folder.
    1. (Optional) Enter a cluster description.
    1. Select the environment where you want to create the cluster (you cannot change the environment once the cluster is created):
        * `PRODUCTION`: For stable versions of your apps.
        * `PRESTABLE`: For testing purposes. The prestable environment is similar to the production environment and likewise covered by the SLA, but it is the first to get new functionalities, improvements, and bug fixes. In the prestable environment, you can test compatibility of new versions with your application.
    1. Select the {{ GP }} version.

    
    1. Optionally, select groups of [dedicated hosts](../../compute/concepts/dedicated-host.md) to host the cluster.

        {% include [Dedicated hosts note](../../_includes/mdb/mgp/note-dedicated-hosts.md) %}

    1. Under **{{ ui-key.yacloud.mdb.forms.section_network }}**:
        * Select the cloud network for the cluster.
        * In the **{{ ui-key.yacloud.mdb.forms.field_security-group }}** parameter, specify the [security group](../operations/connect.md#configuring-security-groups) that contains the rules allowing all incoming and outgoing traffic over any protocol from any IP address.

            {% note alert %}

            For a {{ mgp-name }} cluster to work properly, at least one of its security groups must have rules allowing all incoming and outgoing traffic from any IP address.

            {% endnote %}


        * Select the availability zone and subnet for the cluster. To create a new subnet, click **{{ ui-key.yacloud.common.label_create-new_female }}** next to the availability zone you need.
        * Select **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}** to enable connecting to the cluster from the internet.

    1. (Optional) For clusters with {{ GP }} version 6.25 or higher, enable the **{{ ui-key.yacloud.greenplum.section_cloud-storage }}** option.

        It activates the [{{ YZ }}](https://github.com/yezzey-gp/yezzey/) extension from {{ yandex-cloud }}. This extension is used to export [AO and AOCO tables](../tutorials/yezzey.md) from disks within the {{ mgp-name }} cluster to a cold storage in {{ objstorage-name }}. This way, the data will be stored in a service bucket in a compressed and encrypted form. This is a [more cost-efficient storage method](../../storage/pricing.md).

        You cannot disable this option after you save your cluster settings.

        
        {% note info %}

        This feature is at the [Preview](../../overview/concepts/launch-stages.md) stage and is free of charge.

        {% endnote %}


    1. Specify the admin user settings. This special user is required for managing the cluster and cannot be deleted. For more information, see [Users and roles](../concepts/cluster-users.md).

        * **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}** may contain Latin letters, numbers, hyphens, and underscores, but cannot start with a hyphen. It must be from 1 to 32 characters long.

            {% note info %}

            The names `admin`, `gpadmin`, [mdb_admin](../concepts/cluster-users.md#mdb_admin), `mdb_replication`, `monitor`, `none`, `postgres`, `public`, and `repl` are reserved for {{ mgp-name }}. You cannot create users with these names.

            {% endnote %}

        * **{{ ui-key.yacloud.mdb.forms.database_field_user-password }}** must be from 8 to 128 characters long.

    1. Configure additional cluster settings, if required:

        * {% include [Backup time](../../_includes/mdb/console/backup-time.md) %}
        * **{{ ui-key.yacloud.mdb.forms.maintenance-window-type }}**: [Maintenance window](../concepts/maintenance.md) settings:

            {% include [Maintenance window](../../_includes/mdb/console/maintenance-window-description.md) %}

        * {% include [Datalens access](../../_includes/mdb/console/datalens-access.md) %}


        * {% include [Deletion protection](../../_includes/mdb/console/deletion-protection.md) %}

            {% include [Deletion protection limits db](../../_includes/mdb/deletion-protection-limits-db.md) %}

    1. (Optional) Configure the operating mode and [connection pooler](../concepts/pooling.md) parameters under **{{ ui-key.yacloud.mdb.forms.section_pooler }}**:

        {% include [Pooling mode](../../_includes/mdb/mgp/pooling-mode.md) %}

    1. (Optional) Under **{{ ui-key.yacloud.greenplum.section_background-activities }}**, edit the parameters of [routine maintenance operations](../concepts/maintenance.md#regular-ops):

        {% include [background activities](../../_includes/mdb/mgp/background-activities-console.md) %}

    1. Specify the master host parameters on the **{{ ui-key.yacloud.greenplum.section_resource-master }}** tab. For the recommended configuration, see [Calculating the cluster configuration](calculate-specs.md#master).

        * [{{ ui-key.yacloud.mdb.forms.section_resource }}](../concepts/instance-types.md): Defines technical properties of the virtual machines on which the cluster master hosts will be deployed.

        * Under **{{ ui-key.yacloud.mdb.forms.section_storage }}**:
          * Select the [disk type](../concepts/storage.md).

            {% include [storages-type-no-change](../../_includes/mdb/storages-type-no-change.md) %}

            
            {% include [storages-step-settings](../../_includes/mdb/mgp/settings-storages.md) %}


    1. Specify the parameters of segment hosts on the **{{ ui-key.yacloud.greenplum.section_resource-segment }}** tab. For the recommended configuration, see [Calculating the cluster configuration](calculate-specs.md#segment).

        * Number of segment hosts.
        * [Number of segments per host](../concepts/index.md). The maximum value of this parameter depends on the host class.
        * [Host class](../concepts/instance-types.md): Defines technical properties of the virtual machines on which the cluster segment hosts will be deployed.
        * Under **{{ ui-key.yacloud.mdb.forms.section_storage }}**:
           * Select the [disk type](../concepts/storage.md).

             
             {% include [storages-step-settings](../../_includes/mdb/mgp/settings-storages.md) %}


    1. If required, configure [DBMS cluster-level settings](../concepts/settings-list.md#dbms-cluster-settings).

    1. Click **{{ ui-key.yacloud.common.create }}**.

- CLI {#cli}

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    To create a {{ mgp-name }} cluster:

    
    1. Check whether the folder has any subnets for the cluster hosts:

        ```bash
        yc vpc subnet list
        ```

        If there are no subnets in the folder, [create the required subnets](../../vpc/operations/subnet-create.md) in {{ vpc-short-name }}.


    1. View a description of the create cluster CLI command:

        ```bash
        {{ yc-mdb-gp }} cluster create --help
        ```

    1. Specify cluster parameters in the create command (the list of supported parameters in the example is not exhaustive):

        
        ```bash
        {{ yc-mdb-gp }} cluster create <cluster_name> \
           --greenplum-version=<Greenplum_version> \
           --environment=<environment> \
           --network-name=<network_name> \
           --user-name=<username> \
           --user-password=<user_password> \
           --master-config resource-id=<host_class>,`
                          `disk-size=<storage_size_in_GB>,`
                          `disk-type=<network-hdd|network-ssd|network-ssd-nonreplicated|local-ssd> \
           --segment-config resource-id=<host_class>,`
                          `disk-size=<storage_size_in_GB>,`
                          `disk-type=<network-ssd-nonreplicated|local-ssd> \
           --zone-id=<availability_zone> \
           --subnet-id=<subnet_ID> \
           --assign-public-ip=<public_access_to_hosts> \
           --security-group-ids=<list_of_security_group_IDs> \
           --deletion-protection=<cluster_deletion_protection>
        ```

        {% note info %}

        The cluster name must be unique within a folder. It may contain Latin letters, numbers, hyphens, and underscores. The name may be up to 63 characters long.

        {% endnote %}

        Where:

        * `--greenplum-version`: {{ GP }} version, {{ versions.cli.str }}.
        * `--environment`: Environment:
            * `PRODUCTION`: For stable versions of your apps.
            * `PRESTABLE`: For testing purposes. The prestable environment is similar to the production environment and likewise covered by the SLA, but it is the first to get new functionalities, improvements, and bug fixes. In the prestable environment, you can test compatibility of new versions with your application.
        * `--network-name`: [Network name](../../vpc/concepts/network.md#network).
        * `--user-name`: Username. It may contain Latin letters, numbers, hyphens, and underscores, but must begin with a letter, number, or underscore. It must be from 1 to 32 characters long.
        * `--user-password`: Password. It must be from 8 to 128 characters long.
        * `--master-config` and `--segment-config`: Master and segment host configuration:
            * `resource-id`: [Host class](../concepts/instance-types.md).
            * `disk-size`: Storage size in GB.
            * `disk-type`: [Disk type](../concepts/storage.md):
                * `network-hdd` (for master hosts only)
                * `network-ssd` (for master hosts only)
                * `local-ssd`
                * `network-ssd-nonreplicated`.

        * `--zone-id`: [Availability zone](../../overview/concepts/geo-scope.md).
        * `--subnet-id`: [Subnet ID](../../vpc/concepts/network.md#subnet). Specify if two or more subnets are created in the selected availability zone.
        * `--assign-public-ip`: Flag used if [public access](../concepts/network.md#public-access-to-a-host) to the hosts is required, `true` or `false`.
        * `--security-group-ids`: List of [security group](../../vpc/concepts/security-groups.md) IDs.
        * `--deletion-protection`: Cluster deletion protection, `true` or `false`.


            {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-db.md) %}

    1. To set the start time for the backup, provide the required value in `HH:MM:SS` format under `--backup-window-start`:

        ```bash
        {{ yc-mdb-gp }} cluster create <cluster_name> \
           ...
           --backup-window-start=<backup_start_time>
        ```

    
    1. To create a cluster based on [dedicated host](../../compute/concepts/dedicated-host.md) groups, specify their IDs as a comma-separated list in the `--host-group-ids` parameter:

        ```bash
        {{ yc-mdb-gp }} cluster create <cluster_name> \
           ...
           --host-group-ids=<dedicated_host_group_IDs>
        ```

        {% include [Dedicated hosts note](../../_includes/mdb/mgp/note-dedicated-hosts.md) %}


    1. To set up a [maintenance](../concepts/maintenance.md) window (including for disabled clusters), provide the relevant value in the `--maintenance-window` parameter when creating your cluster:

        ```bash
        {{ yc-mdb-gp }} cluster create <cluster_name> \
           ...
           --maintenance-window type=<maintenance_type>,`
                               `day=<day_of_week>,`
                               `hour=<hour> \
        ```

        Where `type` is the maintenance type:

        {% include [maintenance-window](../../_includes/mdb/cli/maintenance-window-description.md) %}

    1. To enable access from [{{ datalens-full-name }}](../../datalens/concepts/index.md), provide the `true` value in the relevant parameters when creating a cluster:


        
        ```bash
        {{ yc-mdb-gp }} cluster create <cluster_name> \
           ...
           --datalens-access=<access_from_DataLens>
        ```


        Where:

        * `--datalens-access`: Access from {{ datalens-full-name }}, `true` or `false`.


- {{ TF }} {#tf}

  
  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}


  To create a {{ mgp-name }} cluster:

  1. Using the command line, navigate to the folder that will contain the {{ TF }} configuration files with an infrastructure plan. Create the directory if it does not exist.

  
  1. {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. Create a configuration file describing the [cloud network](../../vpc/concepts/network.md#network) and [subnets](../../vpc/concepts/network.md#subnet).

      The cluster is hosted on a cloud network. If you already have a suitable network, you do not need to describe it again.

      Cluster hosts are located on subnets of the selected cloud network. If you already have suitable subnets, you do not need to describe them again.

      Example structure of a configuration file that describes a cloud network with a single subnet:

      ```hcl
      resource "yandex_vpc_network" "<network_name_in_{{ TF }}>" { name = "<network_name>" }

      resource "yandex_vpc_subnet" "<subnet_name_in_{{ TF }}>" {
        name           = "<subnet_name>"
        zone           = "<availability_zone>"
        network_id     = yandex_vpc_network.<network_name_in_{{ TF }}>.id
        v4_cidr_blocks = ["<subnet>"]
      }
      ```


  1. Create a configuration file with a description of the cluster and its hosts.

      Here is an example of the configuration file structure:

      
      ```hcl
      resource "yandex_mdb_greenplum_cluster" "<cluster_name_in_{{ TF }}>" {
        name                = "<cluster_name>"
        environment         = "<environment>"
        network_id          = yandex_vpc_network.<network_name_in_{{ TF }}>.id
        zone                = "<availability_zone>"
        subnet_id           = yandex_vpc_subnet.<subnet_name_in_{{ TF }}>.id
        assign_public_ip    = <public_access_to_cluster_hosts>
        deletion_protection = <cluster_deletion_protection>
        version             = "<Greenplum_version>"
        master_host_count   = <number_of_master_hosts>
        segment_host_count  = <number_of_segment_hosts>
        segment_in_host     = <number_of_segments_per_host>

        master_subcluster {
          resources {
            resource_preset_id = "<host_class>"
            disk_size          = <storage_size_in_GB>
            disk_type_id       = "<disk_type>"
          }
        }

        segment_subcluster {
          resources {
            resource_preset_id = "<host_class>"
            disk_size          = <storage_size_in_GB>
            disk_type_id       = "<disk_type>"
          }
        }

        user_name     = "<username>"
        user_password = "<password>"

        security_group_ids = ["<list_of_security_group_IDs>"]
      }
      ```


      Where:

      * `assign_public_ip`: Public access to cluster hosts, `true` or `false`.
      * `deletion_protection`: Cluster deletion protection, `true` or `false`.
      * `version`: {{ GP }} version.
      * `master_host_count`: Number of master hosts, one or two.
      * `segment_host_count`: Number of segment hosts, between 2 and 32.
      * `segment_in_host`: [Number of segments per host](../concepts/index.md). The maximum value of this parameter depends on the host class.

      Enabled cluster deletion protection will not prevent a manual connection with the purpose to delete database contents.

      For more details about resources you can create using {{ TF }}, see [the provider documentation]({{ tf-provider-mgp }}).

  1. Check that the {{ TF }} configuration files are correct:

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Create a cluster:

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

- API {#api}

    To create a {{ mgp-name }} cluster, use the [create](../api-ref/Cluster/create.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Create](../api-ref/grpc/cluster_service.md#Create) gRPC API call and provide the following in the request:

    * ID of the folder where the cluster should be placed, in the `folderId` parameter.
    * Cluster name in the `name` parameter.
    * Cluster environment in the `environment` parameter.
    * {{ GP }} version in the `config.version` parameter.
    * Username in the `userName` parameter.
    * User password in the `userPassword` parameter.
    * Network ID in the `networkId` parameter.

    
    * [Security group](../concepts/network.md#security-groups) IDs in the `securityGroupIds` parameter.


    * Master host configuration in the `masterConfig` parameter.
    * Segment host configuration in the `segmentConfig` parameter.

    Provide additional cluster settings, if required:

    * Public access settings in the `assignPublicIp` parameter.
    * Backup window settings in the `config.backupWindowStart` parameter.
    * Settings for access from [{{ datalens-full-name }}](../../datalens/concepts/index.md) in the `config.access.dataLens` parameter.
    * Settings for access from [{{ data-transfer-full-name }}](../../data-transfer/) in the `config.access.dataTransfer` parameter.
    * [Maintenance window](../concepts/maintenance.md) settings (including for disabled clusters) in the `maintenanceWindow` parameter.
    * [DBMS settings](../concepts/settings-list.md#dbms-cluster-settings) in the `configSpec.greenplumConfig_<version>` parameter.
    * [Routine maintenance operations](../concepts/maintenance.md#regular-ops) settings in the `configSpec.backgroundActivities.analyzeAndVacuum` parameter.
    * Cluster deletion protection settings in the `deletionProtection` parameter.

        {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-db.md) %}

{% endlist %}

## Creating a cluster copy {#duplicate}

You can create an {{ GP }} cluster with the settings of another one you previously created. To do so, you need to import the configuration of the source {{ GP }} cluster to {{ TF }}. This way, you can either create an identical copy or use the imported configuration as the baseline and modify it as needed. Importing a configuration is a good idea when the source {{ GP }} cluster has a lot of settings and you need to create a similar one.

To create an {{ GP }} cluster copy:

{% list tabs group=instructions %}

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

    1. In the same working directory, place a `.tf` file with the following contents:

        ```hcl
        resource "yandex_mdb_greenplum_cluster" "old" { }
        ```

    1. Write the ID of the initial {{ GP }} cluster to the environment variable:

        ```bash
        export GREENPLUM_CLUSTER_ID=<cluster_ID>
        ```

        You can request the ID with a [list of clusters in the folder](../../managed-greenplum/operations/cluster-list.md#list-clusters).

    1. Import the settings of the initial {{ GP }} cluster into the {{ TF }} configuration:

        ```bash
        terraform import yandex_mdb_greenplum_cluster.old ${GREENPLUM_CLUSTER_ID}
        ```

    1. Get the imported configuration:

        ```bash
        terraform show
        ```

    1. Copy it from the terminal and paste it into the `.tf` file.
    1. Place the file in the new `imported-cluster` directory.
    1. Modify the copied configuration so that you can create a new cluster from it:

        * Specify the new cluster name in the `resource` string and the `name` parameter.
        * Delete the `created_at`, `health`, `id`, `status`, `master_hosts`, and `segment_hosts` parameters.
        * Add the `user_password` parameter.
        * If the `maintenance_window` section has `type = "ANYTIME"`, delete the `hour` parameter.
        * Optionally, make further changes if you need to customize the configuration.

    1. [Get the authentication credentials](../../tutorials/infrastructure-management/terraform-quickstart.md#get-credentials) in the `imported-cluster` directory.

    1. In the same directory, [configure and initialize a provider](../../tutorials/infrastructure-management/terraform-quickstart.md#configure-provider). There is no need to create a provider configuration file manually, you can [download it](https://github.com/yandex-cloud-examples/yc-terraform-provider-settings/blob/main/provider.tf).

    1. Place the configuration file in the `imported-cluster` directory and [specify the parameter values](../../tutorials/infrastructure-management/terraform-quickstart.md#configure-provider). If you did not add the authentication credentials to environment variables, specify them in the configuration file.

    1. Check that the {{ TF }} configuration files are correct:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Examples {#examples}

### Creating a cluster {#create-example}

{% list tabs group=instructions %}

- CLI {#cli}

    Create a {{ mgp-name }} cluster with the following test specifications:

    
    * Name: `gp-cluster`.
    * Version: `{{ versions.cli.latest }}`.
    * Environment: `PRODUCTION`.
    * Network: `default`.
    * User: `user1`.
    * Password: `user1user1`.
    * With master and segment hosts:

        * Class: `s2.medium`
        * With 100 GB local SSD (`local-ssd`) storage

    * Availability zone: `{{ region-id }}-a`; subnet: `{{ subnet-id }}`.
    * With public access to hosts.
    * Security group: `{{ security-group }}`.
    * With protection against accidental cluster deletion.


    Run the following command:

    
    ```bash
    {{ yc-mdb-gp }} cluster create \
       --name=gp-cluster \
       --greenplum-version={{ versions.cli.latest }} \
       --environment=PRODUCTION \
       --network-name=default \
       --user-name=user1 \
       --user-password=user1user1 \
       --master-config resource-id=s2.medium,`
                      `disk-size=100,`
                      `disk-type=local-ssd \
       --segment-config resource-id=s2.medium,`
                       `disk-size=100,`
                       `disk-type=local-ssd \
       --zone-id={{ region-id }}-a \
       --subnet-id={{ subnet-id }} \
       --assign-public-ip=true \
       --security-group-ids={{ security-group }} \
       --deletion-protection=true
    ```


{% endlist %}

{% include [greenplum-trademark](../../_includes/mdb/mgp/trademark.md) %}
