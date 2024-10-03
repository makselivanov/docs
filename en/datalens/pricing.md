---
editable: false
---

# {{ datalens-full-name }} pricing policy



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

## {{ datalens-name }} service plans {#effective-rules}

{{ datalens-full-name }} features service plans offering different service packages:

* **Community**: Suitable for small teams and non-profit projects.
* **Business**: Suitable for enterprise deployments and business scenarios.

The plan you choose covers your organization and can only be [changed](./settings/service-plan.md#change-service-plan) by its owner or administrator.

Service plans offer different feature sets and [cost](#prices) differently.

| **Service plan** | **Community** | **Business** |
------------------ |---------------|---------------
| Interactive dashboards | ![image](../_assets/common/yes.svg) | ![image](../_assets/common/yes.svg) |
| Chart builder | ![image](../_assets/common/yes.svg) | ![image](../_assets/common/yes.svg) |
| Data model and computations | ![image](../_assets/common/yes.svg) | ![image](../_assets/common/yes.svg) |
| Role-based access management | ![image](../_assets/common/yes.svg) | ![image](../_assets/common/yes.svg) |
| Authentication | Yandex ID | Yandex ID, corporate accounts / SSO¹ |
| [{{ datalens-name }} UI customization](./settings/ui-customization.md) | ![image](../_assets/common/no.svg) | ![image](../_assets/common/yes.svg) |
| [{{ datalens-name }} usage statistics](./operations/connection/create-usage-tracking.md) | ![image](../_assets/common/no.svg) | ![image](../_assets/common/yes.svg) |
| [Secure embedding for private objects](./security/private-embedded-objects.md) | ![image](../_assets/common/no.svg) | ![image](../_assets/common/yes.svg) |
| [SLA](https://yandex.com/legal/cloud_sla_datalens) | ![image](../_assets/common/no.svg) | ![image](../_assets/common/yes.svg) |
| Technical support | [Basic plan](../support/pricing.md#base) (if the user does not have [Business](../support/pricing.md#business) or [Premium](../support/pricing.md#premium) enabled) | [Business plan](../support/pricing.md#business) (only applies to {{ datalens-name }}) and priority over the Community plan when processing support requests |

{% note info %}

¹ For existing customers who had configured an identity federation and used a corporate account to log in to {{ datalens-name }} before April 22, 2024, enterprise authentication and SSO will be available for free as part of the _Community_ plan until December 31, 2024.

{% endnote %}

## Prices for the Russia region {#prices}

The cost of using {{ datalens-name }} depends on the service plan you select.

{% note info %}

If you change to the _Business_ plan, the price for the first month will be proportional to the remaining portion of the month as of the change date.

{% endnote %}

A user is active and subject to billing if their actions have resulted in a query to a data source, e.g., they opened or edited a dashboard, chart, or dataset. Public URLs to dashboards and charts work without authentication and do not contribute to active user count. You can look up user count in [{{ datalens-name }} usage statistics](./operations/connection/create-usage-tracking.md).

{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}



{% include notitle [usd.md](../_pricing/datalens/usd.md) %}

