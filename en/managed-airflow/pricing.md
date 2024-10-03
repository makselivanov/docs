---
editable: false
---

# {{ maf-name }} pricing policy



{% note warning %}

{{ maf-name }} is at the [Preview](../overview/concepts/launch-stages.md) stage and is free of charge.

Starting September 16, 2024, the feature enters the [General Availability](../overview/concepts/launch-stages.md) stage and will be charged according to the below [pricing policy](#rules).

Other {{ yandex-cloud }} resources you may use while using the service are charged based on their pricing plans.

{% endnote %}

## What goes into the cost of using {{ maf-name }} {#rules}

When using {{ maf-name }}, you pay for:

* Computing resources of cluster components
* Amount of outgoing traffic

Cluster operation cost is charged per hour. The minimum billing unit is one hour; e.g., you will be charged for 2 hours when using a cluster for 90 minutes (1.5 hours).

Using {{ objstorage-full-name }} for storing [DAG files](operations/upload-dags.md) is charged according to the [{{ objstorage-name }} pricing policy](../storage/pricing.md).

{% include [pricing-gb-size](../_includes/pricing-gb-size.md) %}

## Prices for the Russia region {#prices}




All prices below do not include VAT.


{% include [pricing-month-term](../_includes/mdb/pricing-month-term.md) %}

### Host computing resources {#prices-computing-resources}



{% include [usd-prices](../_pricing/managed-airflow/usd.md) %}


{% include [egress-traffic-pricing](../_includes/egress-traffic-pricing.md) %}
