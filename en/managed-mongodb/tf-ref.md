---
title: "{{ TF }} reference for {{ mmg-full-name }}"
description: "This page provides reference information on the {{ TF }} provider resources and data sources supported for {{ mmg-name }}."
---

# {{ TF }} reference for {{ mmg-full-name }}

{% include [terraform-ref-intro](../_includes/terraform-ref-intro.md) %}

## Resources {#resources}

The following {{ TF }} provider resources are supported for {{ mmg-name }}:

| **{{ TF }} resource** | **{{ yandex-cloud }} resource** |
| --- | --- |
| [yandex_mdb_mongodb_cluster]({{ tf-provider-resources-link }}/mdb_mongodb_cluster) | [Cluster](concepts/index.md) |

## Data sources {#data-sources}

{{ mmg-name }} supports the following {{ TF }} provider data sources:

| **{{ TF }} data source** | **Description** |
| --- | --- |
| [yandex_mdb_mongodb_cluster]({{ tf-provider-datasources-link }}/datasource_mdb_mongodb_cluster) | [Cluster](./concepts/index.md) information |
| [yandex_mdb_mongodb_database]({{ tf-provider-datasources-link }}/datasource_mdb_mongodb_database) | Database information |
| [yandex_mdb_mongodb_user]({{ tf-provider-datasources-link }}/datasource_mdb_mongodb_user) | Database user information |