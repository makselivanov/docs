# Deleting a {{ metastore-full-name }} cluster

You can delete a {{ metastore-name }} cluster along with its stored data.

## Before deleting a cluster {#before-you-delete}

Disable deletion protection for the cluster if it is enabled.

## Delete the cluster {#delete}

{% list tabs group=instructions %}

- Management console {#console}

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_metadata-hub }}**.
   1. In the left-hand panel, select ![image](../../../_assets/console-icons/database.svg) **{{ ui-key.yacloud.metastore.label_metastore }}**.
   1. Click ![image](../../../_assets/console-icons/ellipsis.svg) for the required cluster and select ![image](../../../_assets/console-icons/trash-bin.svg) **{{ ui-key.yacloud.mdb.cluster.overview.button_action-delete }}**.
   1. Confirm deleting the cluster.

{% endlist %}
