---
title: "Resource groups in {{ mgp-full-name }}"
description: "In this tutorial, you will learn what resource groups are, what parameters they have, and how memory is allocated between them."
---

# Resource groups

By using resource groups, users with the `mdb_admin` [role](cluster-users.md#mdb_admin) can set quotas for the following resources allocated for query processing:

* CPU computing resources
* Amount of RAM
* Number of concurrent transactions

A resource group can include multiple [roles](cluster-users.md) that share its quotas.


{{ GP }} gets a transaction query from the user and matches its resource requirements with the unused quotas in the resource group. If the quotas are sufficient, the query execution begins immediately. If not, the query waits for other queries to be completed and to free up the resources. Queries are queued based on the _first in, first out_ (FIFO) principle.

## Resource group parameters {#parameters}

Every resource group has the following parameters:

| Name | Description |
| -----               | ----- |
| `MEMORY_AUDITOR` | The memory auditor used by the resource group. By default, {{ mgp-name }} uses `vmtracker`. |
| `CONCURRENCY` | The maximum number of concurrent transactions a resource group can process. Both active and pending transactions are counted. The default value is `20`. |
| `CPU_RATE_LIMIT` | The percentage of CPU resources allocated to the segment. The minimum value is `1` and the maximum value is `100`. The value is automatically reset to `-1` if the `CPUSET` parameter is specified. |
| `CPUSET` | The numbers of allocated CPU cores. The numbers or their intervals are enclosed in single quotes, e.g., `'1,3-4'`. Use the numbers that exist in the system and are not allocated to other resource groups. The value is automatically reset to `-1` if the `CPU_RATE_LIMIT` parameter is specified. |
| `MEMORY_LIMIT` | The percentage of RAM reserved for the segment. The minimum value is `0` (default) and the maximum one is `100`. When the value is `0`, the resource group can only use [non-allocated RAM](#ram) to run queries. The total `MEMORY_LIMIT` for all resource groups may not exceed `100`. |
| `MEMORY_SHARED_QUOTA` | The allocated memory percentage (`MEMORY_LIMIT`) that can be utilized by all transactions in the resource group when required. The minimum value is `1`, the maximum one is `100`, and the default one is `80`. For more information, see [{#T}](#ram). |
| `MEMORY_SPILL_RATIO` | The RAM usage threshold per transaction operator. When this threshold is reached, temporary files are created on the disk to process the operator. The minimum value is `0` (default) and the maximum one is `100`. If `MEMORY_LIMIT` is `0`, this parameter value must also be `0`. |

## Default resource groups {#default}

Once you create a {{ mgp-name }} cluster, it includes three resource groups:

* `admin_group`: Default group assigned to the `monitor` and `gpadmin` default roles.
* `mdb_admin_group`: Group for administrators. By default, it is assigned to the administrator user whose username and password you specified when creating the cluster.

   If the administrator and other users belong to the same resource group, and all its resources are used up, the administrator will not be able to connect to the database and interrupt resource-intensive processes manually. This is why we recommend assigning this resource group to the users with the `mdb_admin` role only.

* `default_group`: Shared group. It is assigned to a newly created role unless it is assigned another explicit resource group.

Default values of the resource group parameters:

| Parameter | `admin_group` | `mdb_admin_group` | `default_group` |
| --------------------- | ----------- | ----------- | ----------- |
| `MEMORY_AUDITOR` | `vmtracker` | `vmtracker` | `vmtracker` |
| `CONCURRENCY` | `10` | `20` | `20` |
| `CPU_RATE_LIMIT` | `10` | `1` | `30` |
| `CPUSET` | `-1` | `-1` | `-1` |
| `MEMORY_LIMIT` | `10` | `0` | `0` |
| `MEMORY_SHARED_QUOTA` | `80` | `80` | `80` |
| `MEMORY_SPILL_RATIO` | `0` | `0` | `0` |

You cannot change the values of the `admin_group` resource group.

For more information about managing roles, see [Users and roles](./cluster-users.md) and the [{{ GP }} documentation]({{ gp.docs.vmware }}/6/greenplum-database/admin_guide-roles_privs.html).

## Memory allocation {#ram}

* To each resource group, you can allocate the segment's RAM percentage using the `MEMORY_LIMIT` parameter. This percentage is split into the _shared quota_ and _guaranteed quota_:

   * The shared quota is specified by the `MEMORY_SHARED_QUOTA` parameter and can be used by all transactions in the resource group. {{ GP }} allocates the shared quota to transactions based on the _first in, first out_ principle.
   * The guaranteed quota is allocated to each transaction and calculated by the following formula:

      `(MEMORY_LIMIT − MEMORY_SHARED_QUOTA)/CONCURRENCY`

* If the total `MEMORY_LIMIT` of all resource groups is less than `100`, {{ GP }} allocates unallocated RAM to any transactions from any resource groups based on the _first in, first out_ principle.

   The transaction uses non-allocated memory when both conditions are met:

   * The guaranteed memory share for the transaction is used up.
   * The shared quota for the resource group is used up.


   For your cluster to run in a stable and efficient manner, leave 10% to 20% of memory unallocated.

## CPU resource allocation {#cpu}

There are two ways to allocate CPU resources for a resource group:

* (Recommended) Specify the share of resources of the CPU segment to be available for the resource group in the `CPU_RATE_LIMIT` parameter.

   {{ mgp-name }} has a management mode that re-allocates CPU resources from idle resource groups to highly loaded ones. When a resource group is not idle anymore, it regains its re-allocated CPU resources.

* Specify the CPU core numbers to use by the resource group in the `CPUSET` parameter. These cores will not be available to other resource groups. When the resource group is not processing any transactions, the cores allocated to it become idle. We recommend you minimizing the number of such resource groups.

   For stable cluster operation, we do not recommend allocating the `0` core.

You can use these two methods at the same time, for different resource groups, or switch them over as you run your cluster.

For more information about resource groups, see the [{{ GP }} documentation]({{ gp.docs.vmware }}/6/greenplum-database/admin_guide-workload_mgmt_resgroups.html).

{% include [greenplum-trademark](../../_includes/mdb/mgp/trademark.md) %}
