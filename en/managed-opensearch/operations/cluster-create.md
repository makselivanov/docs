---
title: "Creating an {{ OS }} cluster"
description: "A cluster in Yandex Managed Service for {{ OS }} is a group of multiple interlinked {{ OS }} hosts."
keywords:
  - Creating an OpenSearch cluster
  - OpenSearch cluster
  - OpenSearch
---

# Creating an {{ OS }} cluster


A {{ mos-name }} cluster is a group of multiple interlinked {{ OS }} and [dashboards]({{ os.docs }}/dashboards/index/) hosts. A cluster provides high search performance by distributing search and indexing tasks across all cluster hosts with the `DATA` role. To learn more about roles in the cluster, see [Host roles](../concepts/host-roles.md).

Available disk types [depend](../concepts/storage.md) on the selected [host class](../concepts/instance-types.md).

For more information, see [Resource relationships in the service](../concepts/index.md).

## Creating a cluster {#create-cluster}

When creating a cluster, you need to specify individual parameters for each [host group](../concepts/host-roles.md).

To create a {{ mos-name }} cluster, you need the [{{ roles-vpc-user }}](../../vpc/security/index.md#vpc-user) role and the [{{ roles.mos.editor }} role or higher](../security/index.md#roles-list). For more information on assigning roles, see the [{{ iam-name }} documentation](../../iam/operations/roles/grant.md).

{% list tabs group=instructions %}

- Management console {#console}

  To create a {{ mos-name }} cluster:

  1. In the [management console]({{ link-console-main }}), select the folder where you want to create a cluster.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-opensearch }}**.
  1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.
  1. Under **{{ ui-key.yacloud.mdb.forms.section_base }}**:

      1. Enter a name for the cluster. It must be unique within the folder.
      1. (Optional) Enter a cluster description.
      1. Select the environment where you want to create the cluster (you cannot change the environment once the cluster is created):

          * `PRODUCTION`: For stable versions of your apps.
          * `PRESTABLE`: For testing purposes. The prestable environment is similar to the production environment and likewise covered by the SLA, but it is the first to get new functionalities, improvements, and bug fixes. In the prestable environment, you can test compatibility of new versions with your application.

      1. Select the {{ OS }} version.
      1. Select the [plugins](plugins.md#supported-plugins) you want to install in the cluster.

  
  1. Under **{{ ui-key.yacloud.mdb.forms.section_network-settings }}**, select the cloud network to host the cluster and security groups for cluster network traffic. You may also need to [set up security groups](connect.md#security-groups) to connect to the cluster.


   1. Under **{{ ui-key.yacloud.opensearch.cluster.node-groups.title_virtual-node-group }} 1**, configure the [`{{ OS }}` host group](../concepts/host-roles.md):

      1. Select the host group type: `{{ OS }}`

      1. Enter a name for the host group, which must be unique within the cluster.

      1. Select the `DATA` and `MANAGER` [host roles](../concepts/host-roles.md).

      1. Select the platform, host type, and host class.

          The host class defines the technical characteristics of virtual machines that {{ OS }} nodes are deployed on. All available options are listed under [Host classes](../concepts/instance-types.md).

      1. Select the [disk type](../concepts/storage.md) and data storage size.

          {% include [storages-step-settings](../../_includes/mdb/settings-storages-no-broadwell.md) %}

      1. Specify how hosts should be distributed across [availability zones](../../overview/concepts/geo-scope.md) and subnets.

      1. Select the number of hosts to create.

      
      1. Enable **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}** if you want to allow [connecting](connect.md) to hosts over the internet.

          {% note tip %}

          For security reasons, we do not recommend enabling public access for hosts with the `MANAGER` role.

          {% endnote %}


      {% note warning %}

      After creating your cluster, you can only change the host configuration using the API. However, you can also create a new host group with a different configuration if needed.

      {% endnote %}

  1. Configure the `Dashboards` [host group](../concepts/host-roles.md#dashboards) under **{{ ui-key.yacloud.opensearch.cluster.node-groups.title_virtual-node-group }} 2**, if required:

      1. Select the platform, host type, and host class.
      1. Set up storage in the same way as for `{{ OS }}` hosts.
      1. Specify how hosts should be distributed across availability zones and subnets.
      1. Select the number of hosts to create.

      
      1. Enable **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}** if you want to allow [connecting](connect.md) to hosts over the internet.

          {% include [mos-tip-public-dashboards](../../_includes/mdb/mos/public-dashboards.md) %}


  1. If required, click **{{ ui-key.yacloud.opensearch.cluster.node-groups.action_add-virtual-node-group }}** to add another host group or more.

  1. Under **{{ ui-key.yacloud.mdb.forms.section_service-settings }}**:

      1. Enter the password for the `admin` user.

          {% include [Superuser](../../_includes/mdb/mos/superuser.md) %}

      1. If required, change additional cluster settings:

          {% include [Extra settings](../../_includes/mdb/mos/extra-settings.md) %}

  1. Click **{{ ui-key.yacloud.mdb.forms.button_create }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To create a {{ mos-name }} cluster:

  1. View a description of the create cluster CLI command:

      ```bash
      {{ yc-mdb-os }} cluster create --help
      ```

  1. Specify cluster parameters in the create command (the list of supported parameters in the example is not exhaustive):

      ```bash
      {{ yc-mdb-os }} cluster create \
         --name <cluster_name> \
         --description <cluster_description> \
         --labels <labels> \
         --environment <environment:_production_or_prestable> \
         --network-name <network_name> \
         --security-group-ids <security_group_IDs> \
         --service-account-name <service_account_name> \
         --delete-protection <deletion_protection:_true_or_false> \
         --maintenance schedule=<maintenance_type>,`
                      `weekday=<day_of_week>,`
                      `hour=<hour> \
         --version <{{ OS }}_version> \
         --read-admin-password \
         --data-transfer-access=<true_or_false> \
         --serverless-access=<true_or_false> \
         --plugins <{{ OS }}_plugins> \
         --advanced-params <additional_parameters> \
         --opensearch-node-group name=<{{ OS }}_host_group_name>,`
                                `resource-preset-id=<host_class>,`
                                `disk-size=<disk_size_in_bytes>,`
                                `disk-type-id=<network-hdd|network-ssd|network-ssd-nonreplicated|local-ssd>,`
                                `hosts-count=<number_of_hosts_in_group>,`
                                `zone-ids=<availability_zones>,`
                                `subnet-names=<subnet_names>,`
                                `assign-public-ip=<assign_public_address:_true_or_false>,`
                                `roles=<host_roles> \
         --dashboards-node-group name=<Dashboards_host_group_name>,`
                                `resource-preset-id=<host_class>,`
                                `disk-size=<disk_size_in_bytes>,`
                                `disk-type-id=<network-ssd>,`
                                `hosts-count=<number_of_hosts_in_group>,`
                                `zone-ids=<availability_zones>,`
                                `subnet-names=<subnet_names>,`
                                `assign-public-ip=<assign_public_address:_true_or_false>
      ```

      Where:

      * `--labels`: [{{ yandex-cloud }} labels](../../resource-manager/concepts/labels.md) expressed as `<key>=<value>`. You can use them to logically separate resources.
      * `--environment`: Environment.

          * `production`: For stable versions of your apps.
          * `prestable`: For testing purposes. The prestable environment is similar to the production environment and likewise covered by the SLA, but it is the first to get new functionalities, improvements, and bug fixes. In the prestable environment, you can test compatibility of new versions with your application.

      * `--service-account-name`: Name of the service account for [access to {{ objstorage-full-name }}](s3-access.md) as a repository of {{ OS }} snapshots. For more information on service accounts, see the [{{ iam-full-name }} documentation](../../iam/concepts/users/service-accounts.md).

      * `--delete-protection`: Cluster protection from accidental deletion by a user, `true` or `false`. With deletion protection enabled, you still can connect to a cluster manually to delete data.

      * `--maintenance`: Maintenance window settings:

          * To allow maintenance at any time, do not specify the `--maintenance` parameter in the command (default configuration) or specify `--maintenance schedule=anytime`.
          * To specify the preferred start time for maintenance, specify the `--maintenance schedule=weekly,weekday=<day_of_week>,hour=<hour_in_UTC>` parameter in the command. In this case, maintenance will take place every week on a specified day at a specified time.

          Both enabled and disabled clusters undergo maintenance. Maintenance may involve such operations as applying patches or updating DBMS's.

      * `--read-admin-password`: `admin` user password. If you specify this parameter in the command, it will prompt you to enter a password.


      * `--serverless-access`: Access from [{{ serverless-containers-full-name }}](../../serverless-containers/index.yaml), `true` or `false`.
      * `--plugins`: [{{ OS }} plugins](../concepts/plugins.md) to install in the cluster.
      * `--advanced-params`: Additional cluster parameters. The possible values are:

          * `max-clause-count`: Maximum allowed number of boolean clauses per query. For more information, see the [{{ OS }}]({{ os.docs }}/query-dsl/compound/bool/) documentation.
          * `fielddata-cache-size`: JVM heap size allocated for the `fielddata` data structure. You can specify either an absolute value or percentage, e.g., `512mb` or `50%`. For more information, see the [{{ OS }} documentation]({{ os.docs }}/install-and-configure/configuring-opensearch/index-settings/#cluster-level-index-settings).
          * `reindex-remote-whitelist`: List of remote hosts whose indexes contain documents to copy for reindexing. Specify the parameter value as `<host_address>:<port>`. If you need to specify more than one host, list values separated by commas. For more information, see the [{{ OS }} documentation]({{ os.docs }}/im-plugin/reindex-data/#reindex-from-a-remote-cluster).

      {% include [cli-for-os-and-dashboards-groups](../../_includes/managed-opensearch/cli-for-os-and-dashboards-groups.md) %}

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  To create a {{ mos-name }} cluster:

  1. In the configuration file, describe the parameters of the resources you want to create:

      * DB cluster: Description of the {{ mos-name }} cluster and its hosts

      * {% include [Terraform network description](../../_includes/mdb/terraform/network.md) %}

      * {% include [Terraform subnet description](../../_includes/mdb/terraform/subnet.md) %}

      Here is an example of the configuration file structure:

      ```hcl
      resource "yandex_mdb_opensearch_cluster" "<cluster_name>" {
        name                = "<cluster_name>"
        environment         = "<environment>"
        network_id          = "<network_ID>"
        security_group_ids  = ["<list_of_security_group_IDs>"]
        deletion_protection = "<deletion_protection>"

        config {

          version        = "<{{ OS }}_version>"
          admin_password = "<admin_user_password>"

          opensearch {
            node_groups {
              name             = "<virtual_host_group_name>"
              assign_public_ip = <public_access>
              hosts_count      = <number_of_hosts>
              zone_ids         = ["<list_of_availability_zones>"]
              subnet_ids       = ["<list_of_subnet_IDs>"]
              roles            = ["<role_list>"]
              resources {
                resource_preset_id = "<host_class>"
                disk_size          = <storage_size_in_bytes>
                disk_type_id       = "<disk_type>"
              }
            }

            plugins = ["<list_of_plugin_names>"]

          }

          dashboards {
            node_groups {
              name             = "<virtual_host_group_name>"
              assign_public_ip = <public_access>
              hosts_count      = <number_of_hosts>
              zone_ids         = ["<list_of_availability_zones>"]
              subnet_ids       = ["<list_of_subnet_IDs>"]
              resources {
                resource_preset_id = "<host_class>"
                disk_size          = <storage_size_in_bytes>
                disk_type_id       = "<disk_type>"
              }
            }
          }
        }
        maintenance_window {
          type = <maintenance_type>
          day  = <day_of_week>
          hour = <hour>
        }
      }

      resource "yandex_vpc_network" "<network_name>" {
        name = "<network_name>"
      }

      resource "yandex_vpc_subnet" "<subnet_name>" {
        name           = "<subnet_name>"
        zone           = "<availability_zone>"
        network_id     = "<network_ID>"
        v4_cidr_blocks = ["<range>"]
      }
      ```

      Where:

      * `environment`: Environment, `PRESTABLE` or `PRODUCTION`.
      * `deletion_protection`: Deletion protection, `true` or `false`.
      * `assign_public_ip`: Public access to the host, `true` or `false`.
      * `roles`: `DATA` and `MANAGER` host roles.
      * `maintenance_window`: [Maintenance window](../concepts/maintenance.md) settings (including those for disabled clusters):
          * `type`: Maintenance type. The possible values include:
              * `ANYTIME`: Anytime.
              * `WEEKLY`: On a schedule.
          * `day`: Day of the week in `DDD` format for the `WEEKLY` type, e.g., `MON`.
          * `hour`: Hour of the day in `HH` format for the `WEEKLY` type, e.g., `21`.

      {% include [cluster-create](../../_includes/mdb/deletion-protection-limits-db.md) %}

      For a complete list of available {{ mos-name }} cluster configuration fields, see the [{{ TF }} provider documentation]({{ tf-provider-mos }}).

  1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Create a cluster.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      {% include [Terraform timeouts](../../_includes/mdb/mos/terraform/timeouts.md) %}

- API {#api}

  To create a {{ mos-name }} cluster, use the [create](../api-ref/Cluster/create.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Create](../api-ref/grpc/cluster_service.md#Create) gRPC API call and provide the following in the request:

  * ID of the folder where the cluster should be placed, in the `folderId` parameter.
  * Cluster name in the `name` parameter.
  * {{ OS }} version in the `configSpec.version` parameter.
  * `admin` user password in the `configSpec.adminPassword` parameter.
  * Configuration of one or more groups of hosts with the `DATA` and `MANAGER` (optional) [roles](../concepts/host-roles.md) in the `configSpec.opensearchSpec.nodeGroups` parameter.
  * Configuration of one or more groups of hosts with the `DASHBOARDS` [role](../concepts/host-roles.md#dashboards) in the `configSpec.dashboardsSpec.nodeGroups` parameter.
  * List of plugins in the `configSpec.opensearchSpec.plugins` parameter.
  * Settings for access from other services in the `configSpec.access` parameter.
  * Network ID in the `networkId` parameter.

  
  * Security group IDs in the `securityGroupIds` parameter. You may also need to [set up security groups](connect.md#security-groups) to connect to the cluster.
  * ID of the [service account](../../iam/concepts/users/service-accounts.md) used for cluster operations in the `serviceAccountId` parameter.


  * Cluster deletion protection settings in the `deletionProtection` parameter.

      {% include [Deletion protection limits](../../_includes/mdb/deletion-protection-limits-db.md) %}

  * [Maintenance window](../concepts/maintenance.md) settings (including for disabled clusters) in the `maintenanceWindow` parameter.

{% endlist %}

## Creating a cluster copy {#duplicate}

You can create an {{ OS }} cluster with the settings of another one you previously created. To do so, you need to import the configuration of the source {{ OS }} cluster to {{ TF }}. This way, you can either create an identical copy or use the imported configuration as the baseline and modify it as needed. Importing a configuration is a good idea when the source {{ OS }} cluster has a lot of settings and you need to create a similar one.

To create an {{ OS }} cluster copy:

{% list tabs group=instructions %}

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

    1. In the same working directory, place a `.tf` file with the following contents:

        ```hcl
        resource "yandex_mdb_opensearch_cluster" "old" { }
        ```

    1. Write the ID of the initial {{ OS }} cluster to the environment variable:

        ```bash
        export OPENSEARCH_CLUSTER_ID=<cluster_ID>
        ```

        You can request the ID with a [list of clusters in the folder](../../managed-opensearch/operations/cluster-list.md#list-clusters).

    1. Import the settings of the initial {{ OS }} cluster into the {{ TF }} configuration:

        ```bash
        terraform import yandex_mdb_opensearch_cluster.old ${OPENSEARCH_CLUSTER_ID}
        ```

    1. Get the imported configuration:

        ```bash
        terraform show
        ```

    1. Copy it from the terminal and paste it into the `.tf` file.
    1. Place the file in the new `imported-cluster` directory.
    1. Modify the copied configuration so that you can create a new cluster from it:

        * Specify the new cluster name in the `resource` string and the `name` parameter.
        * Delete the `created_at`, `health`, `id`, and `status` parameters.
        * Add the `admin_password` parameter to the `config` section.
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

    {% include [Terraform timeouts](../../_includes/mdb/mos/terraform/timeouts.md) %}

{% endlist %}

## Examples {#examples}

{% list tabs group=instructions %}

- CLI {#cli}

    Create a {{ mos-name }} cluster with the following test specifications:

    * Name: `my-os-clstr`.
    * Description: `My OS cluster`.
    * Label: `label-key` with `label-value`.
    * Environment: `production`.
    * Network name: `{{ network-name }}`.
    * Security group ID: `{{ security-group }}`.
    * Service account name: `os-account`.
    * Cluster deletion protection: Disabled.
    * Maintenance time: Every Monday from 13:00 till 14:00.
    * {{ OS }} version: `2.8`.
    * `admin` user password: Specified after entering the cluster create command.
    * Access to {{ data-transfer-name }}: Enabled.
    * Access to {{ serverless-containers-name }}: Enabled.
    * {{ OS }} added plugin: analysis-icu.
    * {{ OS }} additional parameter: `fielddata-cache-size=50%`.
    * `{{ OS }}` node group configuration:

        * Group name: `os-group`.
        * Host class: `{{ host-class }}`.
        * Disk size: `10737418240` (in bytes).
        * Disk type: `network-ssd`.
        * Number of hosts: Three.
        * Availability zone: `{{ region-id }}-a`.
        * Subnet: `{{ network-name }}-{{ region-id }}-a`.
        * Public address: Assigned.
        * Host group roles: `DATA` and `MANAGER`.

    * `Dashboards` host group configuration:

        * Group name: `dashboard-group`.
        * Host class: `{{ host-class }}`.
        * Disk size: `10737418240` (in bytes).
        * Disk type: `network-ssd`.
        * Number of hosts: One.
        * Availability zone: `{{ region-id }}-a`.
        * Subnet: `{{ network-name }}-{{ region-id }}-a`.
        * Public address: Assigned.

    Run this command:

    ```bash
    {{ yc-mdb-os }} cluster create \
       --name my-os-clstr \
       --description "My OS cluster" \
       --labels label-key=label-value \
       --environment production \
       --network-name {{ network-name }} \
       --security-group-ids {{ security-group }} \
       --service-account-name os-account \
       --delete-protection=false \
       --maintenance schedule=weekly,`
                    `weekday=mon,`
                    `hour=14 \
       --version 2.8 \
       --read-admin-password \
       --data-transfer-access=true \
       --serverless-access=true \
       --plugins analysis-icu \
       --advanced-params fielddata-cache-size=50% \
       --opensearch-node-group name=os-group,`
                              `resource-preset-id={{ host-class }},`
                              `disk-size=10737418240,`
                              `disk-type-id=network-ssd,`
                              `hosts-count=3,`
                              `zone-ids={{ region-id }}-a,`
                              `subnet-names={{ network-name }}-{{ region-id }}-a,`
                              `assign-public-ip=true,`
                              `roles=data+manager \
       --dashboards-node-group name=dashboard-group,`
                              `resource-preset-id={{ host-class }},`
                              `disk-size=10737418240,`
                              `disk-type-id=network-ssd,`
                              `hosts-count=1,`
                              `zone-ids={{ region-id }}-a,`
                              `subnet-names={{ network-name }}-{{ region-id }}-a,`
                              `assign-public-ip=true
    ```

- {{ TF }} {#tf}

    Create a {{ mos-name }} cluster with the following test specifications:

    * Name: `my-os-clstr`.
    * Environment: `PRODUCTION`.
    * {{ OS }} version: `2.8`.
    * `admin` user password: `osadminpwd`.
    * `{{ OS }}` node group name: `os-group`.
    * Host class: `{{ host-class }}`.
    * Disk size: `10737418240` (in bytes).
    * Disk type: `{{ disk-type-example }}`.
    * Number of hosts: `1`.
    * Public address: Assigned.
    * Host group roles: `DATA` and `MANAGER`.
    * Maintenance time: Every Monday from 13:00 till 14:00.
    * Network name: `mynet`.
    * Subnet name: `mysubnet`.
    * Availability zone: `{{ region-id }}-a`.
    * Address range: `10.1.0.0/16`.
    * Security group name: `os-sg`. The security group enables connecting to the cluster host from any network (including the internet) on port `9200`.

    The configuration file for this cluster is as follows:

    ```hcl
    resource "yandex_mdb_opensearch_cluster" "my-os-clstr" {
      name               = "my-os-clstr"
      environment        = "PRODUCTION"
      network_id         = yandex_vpc_network.mynet.id
      security_group_ids = [yandex_vpc_security_group.os-sg.id]

      config {

        version        = "2.8"
        admin_password = "osadminpwd"

        opensearch {
          node_groups {
            name             = "os-group"
            assign_public_ip = true
            hosts_count      = 1
            zone_ids         = ["{{ region-id }}-a"]
            subnet_ids       = [yandex_vpc_subnet.mysubnet.id]
            roles            = ["DATA", "MANAGER"]
            resources {
              resource_preset_id = "{{ host-class }}"
              disk_size          = 10737418240
              disk_type_id       = "{{ disk-type-example }}"
            }
          }
        }
      }
      maintenance_window {
        type = "WEEKLY"
        day  = "MON"
        hour = 14
      }
    }

    resource "yandex_vpc_network" "mynet" {
      name = "mynet"
    }

    resource "yandex_vpc_subnet" "mysubnet" {
      name           = "mysubnet"
      zone           = "{{ region-id }}-a"
      network_id     = yandex_vpc_network.mynet.id
      v4_cidr_blocks = ["10.1.0.0/16"]
    }

    resource "yandex_vpc_security_group" "os-sg" {
      name       = "os-sg"
      network_id = yandex_vpc_network.mynet.id

      ingress {
        description    = "Allow connections to the {{ mos-name }} cluster from the Internet"
        protocol       = "TCP"
        port           = 9200
        v4_cidr_blocks = ["0.0.0.0/0"]
      }

      egress {
        description    = "The rule allows all outgoing traffic"
        protocol       = "ANY"
        v4_cidr_blocks = ["0.0.0.0/0"]
        from_port      = 0
        to_port        = 65535
      }
    }
    ```

{% endlist %}
