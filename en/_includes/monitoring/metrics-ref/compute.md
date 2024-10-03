The metric name is written to the `name` label.

All {{ compute-name }} metrics share the `service=compute` label.

{% note info %}

If the disk name is set, it will be returned in a response from the service. If not, the disk ID will be returned.

{% endnote %}

## Virtual machine and disk metrics {#vm-disk-metrics}

| Metric name<br>Type, unit | Description<br>Labels |
--- | ---
| `cpu_usage`<br>`DGAUGE`, % | VM processor utilization percentage. The value can exceed 100% if the VM consumes more resources than the guaranteed amount.<br>Labels:<br>- *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`. |
| `cpu_utilization`<br>`DGAUGE`, % | VM processor core (vCPU) utilization percentage. Ranges from 0% to the [vCPU performance level](../../../compute/concepts/performance-levels.md). <br>Labels:<br>- *cpu_name*: CPU core ID.<br>- *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`. |
| `disk.io_quota_utilization_percentage`<br>`RATE`, % | Average percentage of the disk quota utilization.<br>Labels:<br>- *disk*: Disk ID or name. |
| `disk.io_quota_utilization_percentage_burst`<br>`RATE`, % | Maximum percentage of the disk quota utilization.<br>Labels:<br>- *disk*: Disk ID or name. |
| `disk.read_bytes`<br>`RATE`, bytes/s | Average number of bytes read from the VM disk.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_bytes_burst`<br>`RATE`, bytes/s | Maximum number of bytes read from the VM disk.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_bytes_in_flight`<br>`DGAUGE`, bytes/s | Average number of bytes being read from the VM disk at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_bytes_in_flight_burst`<br>`DGAUGE`, bytes/s | Maximum number of bytes being read from the VM disk at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_errors`<br>`RATE`, operations/s | Number of failed VM disk read operations.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_latency`<br>`HIST_RATE`, milliseconds | Distribution histogram of the VM disk read requests latency.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_ops`<br>`RATE`, operations/s | Average number of VM disk read operations.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_ops_burst`<br>`RATE`, operations/s | Maximum number of VM disk read operations.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_ops_in_flight`<br>`DGAUGE`, operations/s | Average number of VM disk read operations at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_ops_in_flight_burst`<br>`DGAUGE`, operations/s | Maximum number of VM disk read operations at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.read_throttler_delay`<br>`HIST_RATE`, milliseconds | Histogram of the read operations latency caused by exceeding the VM disk quota.<br>Labels:<br>- *disk*: Disk ID or name. |
| `disk.write_bytes`<br>`RATE`, bytes/s | Average number of bytes written to the VM disk.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_bytes_burst`<br>`RATE`, bytes/s | Maximum number of bytes written to the VM disk.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_bytes_in_flight`<br>`DGAUGE`, bytes/s | Average number of bytes being written to the VM disk at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_bytes_in_flight_burst`<br>`DGAUGE`, bytes/s | Maximum number of bytes being written to the VM disk at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_errors`<br>`RATE`, operations/s | Number of failed VM disk write operations.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_latency`<br>`HIST_RATE`, milliseconds | Distribution histogram of the VM disk write requests latency.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_ops`<br>`RATE`, operations/s | Average number of VM disk write operations.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_ops_burst`<br>`RATE`, operations/s | Maximum number of VM disk write operations.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_ops_in_flight`<br>`DGAUGE`, operations/s | Average number of VM disk write operations at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_ops_in_flight_burst`<br>`DGAUGE`, operations/s | Maximum number of VM disk write operations at the current point in time.<br>Labels:<br>- *disk*: Disk ID or name.<br>- *instance*: VM ID. |
| `disk.write_throttler_delay`<br>`HIST_RATE`, milliseconds | Histogram of the write operations latency caused by exceeding the VM disk quota.<br>Labels:<br>- *disk*: Disk ID or name. |
| `maintenance_event` | `1` if a [maintenance event](../../../compute/concepts/vm-policies) is active on the VM.<br>Labels:<br>- *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`.<br>- *type*: Maintenance event type. The available values are `migrate`, `restart`. |
| `network_connections.quota_utilization`<br>`DGAUGE`, % | VM network connection [quota]({{ link-console-quotas }}) utilization, from 0% to 100%. <br>Labels:<br>- *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`. |
| `network_received_bytes`<br>`DGAUGE`, bytes/s | Number of bytes per second received on the VM network interface.<br>Labels:<br>- *interface_number*: VM network interface ID.<br> - *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`. |
| `network_received_packets`<br>`DGAUGE`, packets/s | Number of packets per second received on the VM network interface.<br>Labels:<br>- *interface_number*: VM network interface ID.<br> - *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`. |
| `network_sent_bytes`<br>`DGAUGE`, bytes/s | Number of bytes per second sent over the VM network interface.<br>Labels:<br>- *interface_number*: VM network interface ID.<br> - *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`. |
| `network_sent_packets`<br>`DGAUGE`, packets/s | Number of packets per second sent over the VM network interface.<br>Labels:<br>- *interface_number*: VM network interface ID.<br>- *resource_id*: VM ID.<br>- *resource_type*: Resource type. The only available value is `vm`. |

## File storage metrics {#fs-metrics}

Common labels for all file storage metrics:

| Label | Value |
----|----
| filestore | [File storage](../../../compute/concepts/filesystem.md) ID |
| instance | [VM](../../../compute/concepts/vm.md) name |

| Metric name<br>Type, unit | Description<br>Labels |
--- | ---  
| `filestore.read_bytes`<br>`RATE`, bytes/s | Average number of bytes read from a file storage |
| `filestore.read_bytes_burst`<br>`DGAUGE`, bytes/s | Maximum number of bytes read from a file storage |
| `filestore.read_errors`<br>`RATE`, operations/s | Number of read operations from a file storage that ended with an error |
| `filestore.read_latency`<br>`RATE`, milliseconds | Histogram of the distribution of processing time for read requests from a file storage. Special `bin` label: Histogram buckets. |
| `filestore.read_ops`<br>`RATE`, operations/s | Average number of read operations from a file storage |
| `filestore.read_ops_burst`<br>`DGAUGE`, operations/s | Maximum number of read operations from a file storage |
| `filestore.write_bytes`<br>`RATE`, bytes/s | Average number of bytes written to a file storage |
| `filestore.write_bytes_burst`<br>`DGAUGE`, bytes/s | Maximum number of bytes written to a file storage |
| `filestore.write_errors`<br>`RATE`, operations/s | Number of write operations to a file storage that ended with an error |
| `filestore.write_latency`<br>`RATE`, milliseconds | Histogram of the distribution of processing time for write requests to a file storage. Special `bin` label: Histogram buckets. |
| `filestore.write_ops`<br>`RATE`, operations/s | Average number of write operations to a file storage |
| `filestore.write_ops_burst`<br>`DGAUGE`, operations/s | Maximum number of write operations to a file storage |

## Instance group metrics {#ig-metrics}

| Metric name<br>Type, unit | Description<br>Labels |
--- | ---                                                                
| `instances_count`<br>`IGAUGE`, pcs | Number of instances in the group.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only value is `instance_group`. |
| `target_instances_count`<br>`IGAUGE`, pcs | Target number of instances in the group.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only value is `instance_group`. |
| `target_utilization`<br>`DGAUGE` | Target utilization of resources per VM instance.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *source_metric*: Metric name. |
| `summary_capacity`<br>`DGAUGE` | Total utilization of resources for all instances, which causes an increase in the group size.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *source_metric*: Metric name. |
| `summary_utilization`<br>`DGAUGE` | Total utilization of resources for all instances.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *source_metric*: Metric name. |
| `average_utilization`<br>`DGAUGE` | Average utilization of resources for all instances.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *source_metric*: Metric name. |
| `target_workload`<br>`DGAUGE` | Target workload on a VM instance in the group or availability zone.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *source_metric*: Metric name. |
| `instances_count_in_zone`<br>`IGAUGE`, pcs | Number of VM instances in the availability zone.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *zone_id*: Availability zone ID. |
| `target_instances_count_in_zone`<br>`IGAUGE`, pcs | Target number of VM instances in the availability zone.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *zone_id*: Availability zone ID. |
| `average_utilization_in_zone`<br>`DGAUGE` | Average utilization of resources for all instances in the availability zone.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *zone_id*: Availability zone ID.<br>- *source_metric*: Metric name. |
| `utilization_in_zone`<br>`DGAUGE` | Total utilization of resources for all instances in the availability zone.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *zone_id*: Availability zone ID.<br>- *source_metric*: Metric name. |
| `summary_capacity_in_zone`<br>`DGAUGE` | Total utilization of resources for all instances in the availability zone, which causes an increase in the group size.<br>Labels<br>:- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *zone_id*: Availability zone ID.<br>- *source_metric*: Metric name. |
| `zone_workload`<br>`DGAUGE` | Workload on a VM instance in the availability zone.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *zone_id*: Availability zone ID.<br>- *source_metric*: Metric name. |
| `region_workload`<br>`DGAUGE` | Workload on a VM instance in the group.<br>Labels:<br>- *resource_id*: Instance group ID.<br>- *resource_type*: Resource type. The only available value is `instance_group`.<br>- *source_metric*: Metric name. |
