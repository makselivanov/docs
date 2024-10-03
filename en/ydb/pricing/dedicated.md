---
title: "Pricing rules for the {{ ydb-full-name }} mode with dedicated instances"
description: "In this tutorial, you will learn about the pricing policy for the {{ ydb-full-name }} mode with dedicated instances."
editable: false
---

# Pricing rules for the {{ ydb-name }} mode with dedicated instances



{% include [use-calculator](../../_includes/pricing/use-calculator.md) %}

{% include [currency-choice](../_includes/pricing/currency-choice.md) %}

{% include [pricing-status.md](../_includes/pricing/pricing-status.md) %}

## What goes into the cost of using {{ ydb-name }} {#rules}

When using {{ ydb-name }} in dedicated mode, you pay for the following:
* Type and size of [storage groups](../concepts/resources.md#storage-groups) allocated for the DB.
* [Computing resources](../concepts/resources.md#resource-presets) allocated to the DB.

Other consumed resources to be additionally paid for:
* Space used in {{ objstorage-full-name }} to store on-demand backups.
* Egress traffic from {{ yandex-cloud }}.

{% include [pricing-gb-size](../_includes/pricing/pricing-gb-size.md) %}

### Using computing resources {#rules-hosts-uptime}

The cost is calculated for each hour of VM operation in accordance with its class. To view the specifications for each class, see [{#T}](../concepts/index.md).

The minimum billing unit is one hour; for example, you will be charged for 2 hours when using a VM for 90 minutes (1.5 hours).

### Disk space usage {#rules-storage}

You pay for the following:
* Storage allocated for DB storage groups.
* Space used by on-demand DB backups saved in {{ objstorage-name }}.

   {% note info %}

   For each DB, {{ ydb-name }} automatically creates and stores two full backups for the last two days for free. No fee is charged for storing automatic backups.

   {% endnote %}

The price covers one month of use. The minimum billing unit is 1 GB per hour (e.g., storing 1 GB for 1.5 hours costs the same as for 2 hours).


## Discount for committed volumes of services (CVoS) {#cvos}

{% include [cvos](../../_includes/mdb/cvos.md) %}

{{ ydb-name }} provides two kinds of CVoS: on vCPUs and RAM on the hosts you plan to use in DB clusters. In the management console, you can see potential savings from using a CVoS at the current resource usage. You can also forecast your monthly payments for the desired number of vCPUs and RAM.

{% note info %}

Currently, you cannot order storage or web traffic this way.

{% endnote %}


## Prices for the Russia region {#prices}

{% include [pricing-diff-regions](../../_includes/pricing-diff-regions.md) %}


### Host computing resources {#prices-compute-units}



{% include [usd-compute-units.md](../../_pricing/ydb/usd-compute-units.md) %}


### Storage and backups {#prices-storage}



{% include notitle [usd-storage.md](../../_pricing/ydb/usd-storage.md) %}


{% include [egress-traffic-pricing](../../_includes/egress-traffic-pricing.md) %}
