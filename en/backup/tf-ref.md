---
title: "{{ TF }} reference for {{ backup-full-name }}"
description: "This page provides reference information on the Terraform provider resources supported for {{ backup-name }}."
---

# {{ TF }} reference for {{ backup-full-name }}

{% include [terraform-ref-intro](../_includes/terraform-ref-intro.md) %}

## Resources {#resources}

The following {{ TF }} provider resources are supported for {{ backup-name }}:

| **{{ TF }} resource** | **{{ yandex-cloud }} resource** |
| --- | --- |
| [yandex_backup_policy]({{ tf-provider-resources-link }}/backup_policy) | [Backup policy](./concepts/policy.md) |
| [yandex_backup_policy_bindings]({{ tf-provider-resources-link }}/backup_policy_bindings) | Linking a backup policy to a [VM](../compute/concepts/vm.md) |

## Data sources {#data-sources}

{{ billing-name }} supports the following {{ TF }} provider data sources:

| **{{ TF }} data source** | **Description** |
| --- | --- |
| [yandex_backup_policy]({{ tf-provider-datasources-link }}/datasource_backup_policy) | Backup policy information |