# Stopping and starting an {{ KF }} cluster

You can stop and restart an {{ KF }} cluster as needed. When stopped, a cluster retains all its data. The data will be available again as soon as you restart the cluster.

## Stopping a cluster {#stop-cluster}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), go to the relevant folder.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kafka }}**.
  1. Find the cluster in the list, click ![options](../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud.mdb.clusters.button_action-stop }}**.
  1. In the dialog box that opens, confirm that you want to stop the cluster and click **{{ ui-key.yacloud.mdb.cluster.stop-dialog.popup-confirm_button }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To stop a cluster, run the command:

  ```bash
  {{ yc-mdb-kf }} cluster stop <cluster_name_or_ID>
  ```


- API {#api}

  To stop a cluster, use the [stop](../api-ref/Cluster/stop.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Stop](../api-ref/grpc/cluster_service.md#Stop) gRPC API call and provide the cluster ID in the `clusterId` request parameter.

  To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).


{% endlist %}

## Starting a cluster {#start-cluster}

You can restart clusters with the `Stopped` status.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), go to the relevant folder.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kafka }}**.
  1. In the list, find the relevant stopped cluster, click ![options](../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud.mdb.clusters.button_action-start }}**.
  1. In the window that opens, click **{{ ui-key.yacloud.mdb.cluster.start-dialog.popup-confirm_button }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  To start a cluster, run the command:

  ```bash
  {{ yc-mdb-kf }} cluster start <cluster_name_or_ID>
  ```


- API {#api}

  To start a cluster, use the [start](../api-ref/Cluster/start.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Start](../api-ref/grpc/cluster_service.md#Start) gRPC API call and provide the cluster ID in the `clusterId` request parameter.

  To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).


{% endlist %}
