# Viewing backup statistics

{{ backup-name }} automatically delivers metrics on the number of protected VMs and the storage space used by backups to [{{ monitoring-full-name }}](../../monitoring/).

To view the statistics:

{% list tabs group=instructions %}

- Management console {#console}

   1. In the [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) where the backup [policy](../concepts/policy.md) has been created.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_monitoring }}**.
   1. Select the dashboard: **{{ backup-name }}**.
   1. In the **Resource name** field, select the VM you want to view the statistics for.

      If you select `*` in this field, the dashboard will display aggregate statistics for all VMs.

   1. Select the time period to view the statistics for.
   1. To refresh a dashboard, click ![](../../_assets/console-icons/arrows-rotate-right.svg). Next to the button, you can also set the statistics auto refresh rate.

{% endlist %}

#### See also {#see-also}

* [{#T}](../metrics.md)
