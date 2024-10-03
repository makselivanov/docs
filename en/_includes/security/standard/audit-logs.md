# 5. Collecting, monitoring, and analyzing audit logs


#### Introduction {#intro}

An audit log is a record of all events in the system, including access to it and operations performed. By collecting and verifying audit logs, you can monitor compliance with the established security procedures and standards and identify vulnerabilities in your security mechanisms.

Events in audit logs occur on different levels:
* [{{ yandex-cloud }} level](#cloud-level): Events that occur with {{ yandex-cloud }} resources.
* [OS level](#os-level).
* [Application level](#app-level).
* [Network level](#network-level) (Flow Logs).

{% note info %}

For more information about {{ k8s }} events, see [Collecting, monitoring, and analyzing audit logs in {{ managed-k8s-full-name }}](../../../security/domains/kubernetes.md#collection-monitoring-analysis-audit-logs).

{% endnote %}

#### 5.1 {{ at-full-name }} is enabled at the organization level {#audit-trails}

The main tool for collecting {{ yandex-cloud }} level logs is [{{ at-full-name }}](../../../audit-trails/concepts/index.md). This service allows you to collect audit logs about events happening to {{ yandex-cloud }} resources and upload these logs to {{ objstorage-full-name }} buckets or {{ cloud-logging-name }} log groups for further analysis or export. See [this guide](../../../audit-trails/quickstart.md) on how to start collecting logs. You can also learn more about the [event format](../../../audit-trails/concepts/format.md) or check out the [event reference](../../../audit-trails/concepts/events.md).

To collect metrics, analyze {{ yandex-cloud }}-level events, and set up notifications, we recommend using [{{ monitoring-full-name }}](../../../monitoring/). It helps you track, for example, a sharp increase in the load on {{ compute-name }}, the number of {{ alb-name }} requests per second (RPS), or significant changes in event statistics in {{ iam-name }}.

You can also use {{ monitoring-name }} to monitor the health of the {{ at-name }} service itself and track security events. You can export metrics to a SIEM system via the API, see the [instructions](../../../monitoring/operations/metric/get.md).

[Solution: Monitoring {{ at-name }} and security events using {{ monitoring-name }}](https://github.com/yandex-cloud-examples/yc-audit-trails-monitoring)

You can export audit logs to a [{{ cloud-logging-name }}](../../../logging/) or {{ yds-name }} log group and to a customer's SIEM system to analyze information about events and incidents.

List of important {{ yandex-cloud }}-level events to search for in audit logs:

[Solution: Searching for important security events in audit logs](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/auditlogs/_use_cases_and_searches)

You can enable {{ at-full-name }} at the folder, cloud, and organization level. We recommend enabling {{ at-full-name }} at the level of the entire organization. Thus you will be able to collect audit logs in a centralized manner, e.g., to a separate security cloud.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

   1. In the management console, select the cloud or folder to check the functions in.
   1. In the list of services, select {{ at-full-name }}.
   1. Make sure the Filter parameter is set to Organization.
   1. In addition, check that the destination of logs is {{ objstorage-full-name }} bucket, [{{ cloud-logging-name }}](../../../logging/) log group, and {{ yds-name }}, that they are up and running, and that the logs are available for further analysis.

{% endlist %}

#### 5.2 {{ at-full-name }} events are exported to SIEM systems {#events}

Solutions for exporting {{ yandex-cloud }} audit logs are available for the following SIEM systems:


* ArcSight: [Collecting, monitoring, and analyzing audit logs in ArcSight SIEM](https://github.com/yandex-cloud-examples/yc-export-auditlogs-to-arcsight)

* Splunk: [Collecting, monitoring, and analyzing audit logs in Splunk SIEM](https://github.com/yandex-cloud-examples/yc-export-auditlogs-to-splunk)

* MaxPatrol SIEM: [Collecting, monitoring, and analyzing audit logs in MaxPatrol SIEM](../../../audit-trails/tutorials/maxpatrol.md)

* Wazuh: [Collecting, monitoring, and analyzing audit logs in Wazuh](https://github.com/yandex-cloud-examples/yc-export-auditlogs-to-wazuh/blob/main/README-en.md)

For more information about MaxPatrol, see this [section](../../../audit-trails/tutorials/maxpatrol.md).

You can set up export to any SIEM using [GeeseFS](../../../storage/tools/geesefs.md) or [s3fs](../../../storage/tools/s3fs.md). These utilities allow mounting a {{ objstorage-full-name }} bucket as a VM local disk. Next, you need to install a SIEM connector on your VM and configure reading JSON files from the bucket. You can also use utilities compatible with AWS Kinesis datastreams if sending audit logs to {{ yds-full-name }}.

If you have no SIEM, you can also analyze audit logs manually using one of the following methods (in descending order of convenience):

* [Searching](../../../audit-trails/tutorials/query.md) for {{ yandex-cloud }} events in {{ yq-full-name }}.


* [Searching](../../../audit-trails/tutorials/search-cloud-logging.md) for {{ yandex-cloud }} events in {{ cloud-logging-name }}.
* [Searching](../../../audit-trails/tutorials/search-bucket.md) for {{ yandex-cloud }} events in {{ objstorage-name }}.

{% list tabs group=instructions %}

- Manual check {#manual}

   Make sure that audit logs from {{ at-full-name }} are exported for analysis to a SIEM system or analyzed in the cloud using one of the available methods.

{% endlist %}

#### 5.3 Responding to {{ at-full-name }} events is set up {#reaction}

You can respond to {{ at-full-name }} events using your SIEM tools or manually. You can also use automatic responses.

Using {{ sf-full-name }}, you can configure alerts about {{ at-name }} events, as well as automatic responses to malicious actions, including removing dangerous rules or revoking access rights.

[Solution: Notifications and responses to {{ at-name }} information security events using {{ iam-short-name }} / {{ sf-name }} + Telegram](https://github.com/yandex-cloud-examples/yc-audit-trails-automatic-response)

#### 5.4 Hardening of the Object Storage bucket that stores {{ at-full-name }} audit logs is done {#hardering}

If you write {{ at-full-name }} audit logs to a {{ objstorage-full-name }} bucket, make sure the bucket is set up using best security practices, such as:

* 4.1 In {{ objstorage-full-name }}, encryption of data at rest using KMS keys is enabled.
* 3.8 In {{ objstorage-full-name }}, logging of actions with buckets is enabled.
* 3.8 In {{ objstorage-full-name }}, the **Object locks** feature is enabled.
* 3.7 In {{ objstorage-full-name }}, Bucket Policies are used.
* 3.6 No public access to the {{ objstorage-full-name }} bucket is allowed.

You can use a solution for secure {{ objstorage-full-name }} bucket setup with {{ TF }}.

{% list tabs group=instructions %}

- Manual check {#manual}

   Run a manual check.

{% endlist %}

#### 5.5 Audit logs are collected at the OS level {#os-collection}

When using IaaS cloud services and {{ k8s }} node groups, the customer is responsible for ensuring OS security and collecting OS-level events on their own. Free tools for collecting standard OS-generated events and exporting them to the customer's SIEM system include:
* [Osquery](https://osquery.io/)
* [Filebeat (ELK)](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-system.html)
* [Wazuh](https://documentation.wazuh.com/current/getting-started/use_cases/log_analysis.html)

Additional event generation options can be implemented using Auditd for Linux or Sysmon for Windows.

You can collect Linux system metrics (CPU, RAM, and disk space usage) with [{{ unified-agent-short-name }}](../../../monitoring/concepts/data-collection/unified-agent/index.md) in {{ monitoring-name }}.

You can also export OS events to {{ cloud-logging-name }} using a [Fluent Bit plugin](https://github.com/yandex-cloud/fluent-bit-plugin-yandex) or to {{ yds-name }}.

To describe events to be searched for in audit logs, we recommend using [Sigma](https://github.com/SigmaHQ/sigma) format, which is supported by popular SIEM systems. The Sigma repository contains a [library of events](https://github.com/SigmaHQ/sigma/tree/master/rules) described in this format.

To get the exact time of OS- and application-level events, configure clock synchronization by following [this guide](../../../compute/tutorials/ntp.md).

{% list tabs group=instructions %}

- Manual check {#manual}

   Run a manual check.

{% endlist %}

#### 5.6 Audit logs are collected at the application level {#app-level}

Customers may collect events that occur at the level of applications deployed on {{ compute-short-name }} resources on their own. For example, save application logs to files and transfer them to a SIEM system using the tools listed in the subsection above.

{% list tabs group=instructions %}

- Manual check {#manual}

   Run a manual check.

{% endlist %}

#### 5.7 Logs are collected at the network level {#network-level}

Currently, {{ vpc-short-name }} network traffic event logs (Flow Logs) can only be collected by customers. You can use {{ marketplace-full-name }} solutions (such as [NGFW](/marketplace?tab=software&search=NGFW), [IDS/IPS](/marketplace?tab=software&search=IDS%2FIPS), or [network products](/marketplace?categories=network)) or free software for collecting and transmitting events. You can also collect network-level logs using different agents, e.g., HIDS.

{% list tabs group=instructions %}

- Manual check {#manual}

   Run a manual check.

{% endlist %}
