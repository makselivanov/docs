# Getting started with {{ backup-full-name }}

{{ backup-name }} is a service for creating backups and restoring {{ yandex-cloud }} resources and their data.

Create a {{ compute-full-name }} [VM](../compute/concepts/vm.md) with {{ backup-name }} connection and link it to a [backup policy](./concepts/policy.md).

For more information on the minimum required VM characteristics and supported operating systems, see [{#T}](./concepts/vm-connection.md).

To get started with {{ backup-name }}:
1. [Prepare your cloud](#before-you-begin).
1. [Create an infrastructure](#deploy-infrastructure).
1. [Activate {{ backup-name }}](#activate-provider).
1. [Create a VM](#vm-create).
1. [Link your VM to a backup policy](#add-policy).

If you no longer need the resources you created, [delete them](#clear-out).

## Prepare your cloud {#before-you-begin}

{% include [before-you-begin](../_tutorials/_tutorials_includes/before-you-begin.md) %}

### Required paid resources {#paid-resources}

The cost of VM backup resources includes:
* Fee for VM [computing resources](../compute/concepts/vm-platforms.md) and [disks](../compute/concepts/disk.md) (see [{{ compute-full-name }} pricing](../compute/pricing.md)).
* Fee for protected VMs and backup storage (see [{{ backup-full-name }} pricing](./pricing.md)).

## Create an infrastructure {#deploy-infrastructure}

### Prepare a network {#network-setup}

You can use an existing [cloud network](../vpc/concepts/network.md#network) and [subnet](../vpc/concepts/network.md#subnet) or create new ones.

{% cut "Creating a cloud network with subnets" %}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), go to the folder you want to create a VM with a {{ backup-name }} connection in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
  1. Click **{{ ui-key.yacloud.vpc.networks.button_create }}**.
  1. In the **{{ ui-key.yacloud.vpc.networks.create.field_name }}** field, enter a name for the network. The naming requirements are as follows:

      {% include [name-format](../_includes/name-format.md) %}

  1. Keep **{{ ui-key.yacloud.vpc.networks.create.field_is-default }}** enabled.
  1. Click **{{ ui-key.yacloud.vpc.networks.button_create }}**.

{% endlist %}

{% endcut %}

For more information, see [{#T}](../vpc/operations/network-create.md).

### Configure the security group {#sg-setup}

You can use an existing [security group](../vpc/concepts/security-groups.md) or create a new one.

{% cut "Creating a new security group" %}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), go to the folder you want to create a VM with a {{ backup-name }} connection in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
  1. In the left-hand panel, select ![image](../_assets/console-icons/shield.svg) **{{ ui-key.yacloud.vpc.switch_security-groups }}**.
  1. Click **{{ ui-key.yacloud.vpc.network.security-groups.button_create }}**.
  1. Enter a name for the security group.
  1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-network }}** field, select the network that the security group will refer to.
  1. Click **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

{% endcut %}

Add VM outgoing traffic [rules](../vpc/concepts/security-groups.md#security-groups-rules) to the security group:

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), go to the folder you want to create a VM with a {{ backup-name }} connection in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
  1. In the left-hand panel, select ![image](../_assets/console-icons/shield.svg) **{{ ui-key.yacloud.vpc.switch_security-groups }}**.
  1. Next to the security group you want to add rules to, click ![image](../_assets/console-icons/ellipsis.svg) and select ![image](../_assets/console-icons/pencil.svg) **{{ ui-key.yacloud.common.edit }}**.
  1. Under **{{ ui-key.yacloud.vpc.network.security-groups.forms.label_section-rules }}**, go to the **{{ ui-key.yacloud.vpc.network.security-groups.label_egress }}** tab and click **{{ ui-key.yacloud.vpc.network.security-groups.button_add-rule }}**.
  1. Add the following outgoing traffic rules one by one:

      {% include [outgoing-traffic](../_includes/backup/outgoing-rules.md) %}

  1. Click **{{ ui-key.yacloud.common.save }}**.

{% endlist %}

For more information, see [{#T}](../vpc/operations/security-group-create.md).

### Set up a service account {#prepare-service-account}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder you want to create a VM with a {{ backup-name }} connection in.
  1. At the top of the page, go to the **{{ ui-key.yacloud.iam.folder.switch_service-accounts }}** tab.
  1. Click **{{ ui-key.yacloud.iam.folder.service-accounts.button_add }}**.
  1. Enter a name for the [service account](../iam/concepts/users/service-accounts.md). The name format requirements are as follows:

      {% include [name-format](../_includes/name-format.md) %}

  1. Click ![plus-sign](../_assets/console-icons/plus.svg) **{{ ui-key.yacloud.iam.folder.service-account.label_add-role }}** and select the `backup.editor` [role](./security/index.md#backup-editor).
  1. Click **{{ ui-key.yacloud.iam.folder.service-account.popup-robot_button_add }}**.

{% endlist %}

For more information, see [{#T}](../iam/operations/sa/create.md).

## Activate {{ backup-name }} {#activate-provider}

To activate the service, you need _at least_ the `backup.editor` [role](security/index.md#backup-editor) for the [folder](../resource-manager/concepts/resources-hierarchy.md#folder) where you want to create a VM with a {{ backup-name }} connection.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder you want to create a VM with a {{ backup-name }} connection in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_backup }}**.
  1. If you have not activated {{ backup-name }} yet, click **{{ ui-key.yacloud.backup.button_action-activate }}**.

      If there is no **{{ ui-key.yacloud.backup.button_action-activate }}** button, and you have access to creating a VM with a {{ backup-name }} connection, it means the service has already been activated. Proceed to the next step.

{% endlist %}

## Create a VM {#vm-create}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder you want to create a VM with a {{ backup-name }} connection in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
  1. In the left-hand panel, select ![image](../_assets/console-icons/server.svg) **{{ ui-key.yacloud.compute.switch_instances }}** and click **{{ ui-key.yacloud.compute.instances.button_create }}**.
  1. Under **{{ ui-key.yacloud.compute.instances.create.section_base }}**:

      1. Enter the VM name. The naming requirements are as follows:

          {% include [name-format](../_includes/name-format.md) %}

      1. Select an [availability zone](../overview/concepts/geo-scope.md) to place your VM in.

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_image }}**, select one of the [supported operating systems](./concepts/vm-connection.md#os), e.g., [Ubuntu 20.04](/marketplace/products/yc/ubuntu-20-04-lts).

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_storages_ru }}** and **{{ ui-key.yacloud.compute.instances.create.section_platform }}**, set the preferred VM parameters.

      {% include [vm-requirements](../_includes/backup/vm-requirements.md) %}

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_network }}**:

      1. In the **{{ ui-key.yacloud.component.compute.network-select.field_subnetwork }}** field, select the subnet you [prepared earlier](#network-setup).
      1. In the **{{ ui-key.yacloud.component.compute.network-select.field_external }}** field, select `{{ ui-key.yacloud.component.compute.network-select.switch_auto }}`.

          Instead of assigning a public IP address to your VM, you can link the subnet hosting this VM to a [route table](../vpc/concepts/routing.md#rt-vm) allowing internet access via a [NAT gateway](../vpc/concepts/gateways.md) or a custom router.

      1. In the **{{ ui-key.yacloud.component.compute.network-select.field_security-groups }}** field, select the security group you [configured previously](#sg-setup).

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_access }}**, specify the data for access to the VM:

      1. Select the service account you [created earlier](#prepare-service-account).
      1. Enter the username into the **{{ ui-key.yacloud.compute.instances.create.field_user }}** field.
      1. In the **{{ ui-key.yacloud.compute.instances.create.field_key }}** field, paste the contents of the public key file.

          You will need to create a key pair for the SSH connection yourself; see [{#T}](../compute/operations/vm-connect/ssh.md#creating-ssh-keys) for details.

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_additional }}**:

      {% include [backup-enable](../_includes/compute/backup-enable.md) %}

  1. Click **{{ ui-key.yacloud.compute.instances.create.button_create }}**.

{% endlist %}

For more information, see [{#T}](../compute/operations/index.md#vm-create).

## Link your VM to a backup policy {#add-policy}

{% include [agent-installation-timespan](../_includes/backup/agent-installation-timespan.md) %}

1. Make sure the {{ backup-name }} agent has been installed:

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder the service is activated in.
      1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
      1. Select the appropriate VM.
      1. Check that the value of the **{{ ui-key.yacloud.backup.field_vm-instances }}** field in the **{{ ui-key.yacloud.backup.title_backup }}** section is `{{ ui-key.yacloud.backup.label_create }}`.

    {% endlist %}

    {% include [vm-list](../_includes/backup/vm-list.md) %}

    {% include [agent-installation-failure](../_includes/backup/agent-installation-failure.md) %}

1. Link your VM to a backup policy:

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder the service is activated in.
      1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_backup }}**.
      1. Go to the ![policies](../_assets/console-icons/calendar.svg) **{{ ui-key.yacloud.backup.label_policies }}** tab.
      1. Select one of the policies created by default.
      1. Under **{{ ui-key.yacloud.backup.label_linked-instances }}**, click ![plus-sign](../_assets/console-icons/plus.svg) **{{ ui-key.yacloud.backup.button_attach-instance }}**.
      1. Select the VM from the list and click **{{ ui-key.yacloud.backup.button_attach-instance-submit }}**.

    {% endlist %}

    You can also link your VM to a backup policy in {{ compute-name }}:

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder the service is activated in.
      1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
      1. Select the appropriate VM.
      1. Under **{{ ui-key.yacloud.backup.title_backup }}**, click ![image](../_assets/console-icons/pencil.svg) in the **{{ ui-key.yacloud.backup.label_policies }}** field.
      1. Select one of the policies created by default and click **{{ ui-key.yacloud.common.save }}**.

    {% endlist %}

## How to delete the resources you created {#clear-out}

To stop paying for the resources you created:
1. [Delete](./operations/delete-vm.md) the VM from {{ backup-name }}.
1. [Delete](../compute/operations/vm-control/vm-delete.md) the VM from {{ compute-name }}.
1. [Delete](./operations/backup-vm/delete.md) VM backups, if any.

## What's next {#what-is-next}

* [Learn about service features](./concepts/index.md).
* [See other service guides](./operations/index.md).