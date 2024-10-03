---
title: "Access management in {{ ydb-full-name }}"
description: "Access management in the {{ ydb-full-name }} database creation and management service. To allow access to {{ ydb-short-name }} resources (databases and their users), assign appropriate roles from the list to the user."
sourcePath: overlay/security/start_auth.md
---

# Access management in {{ ydb-name }}

{{ yandex-cloud }} users can only perform operations on resources that are allowed by the roles assigned to them. As long as a user has no roles assigned, almost all operations are forbidden.

To enable access to {{ ydb-short-name }} resources (databases and their users), assign the required roles from the list below to a Yandex account, [service account](../../iam/concepts/users/service-accounts.md), [federated users](../../iam/concepts/federations.md), [user group](../../organization/operations/manage-groups.md), [system group](../../iam/concepts/access-control/system-group.md), or [public group](../../iam/concepts/access-control/public-group.md). A role can be assigned for a parent resource (folder or cloud) whose roles are inherited by nested resources.

You can also grant a role to access a specific database. This will allow a user without any roles in the directory hosting the database to access the database according to the role assigned.

Roles for a resource can be assigned by users who have the `ydb.admin` role or one of the following roles for that resource:

{% include [roles-list](../../_includes/iam/roles-list.md) %}

{% note info %}

For more information about role inheritance, see [{#T}](../../resource-manager/concepts/resources-hierarchy.md#access-rights-inheritance) in the {{ resmgr-full-name }} documentation.

{% endnote %}

## Assigning roles {#grant-roles}

To assign a user a role:

{% include [grant-role-console](../../_includes/grant-role-console.md) %}

## Which resources you can assign a role for {#resources}

{% include [basic-resources](../../_includes/iam/basic-resources-for-access-control.md) %}

You can also assign a role for individual resources of the service. The list of such resources depends on the {{ yandex-cloud }} interfaces you use to assign roles:

* In the management console, you can only assign roles for a [YDB DBMS](../concepts/index.md#ydb).
* Via the YC CLI or {{ yandex-cloud }} API, you can assign roles for a DBMS and its backup.

## Which roles exist in the service {#roles-list}

The list below shows all roles considered when verifying access permissions in {{ ydb-name }}.

### Service roles {#service-roles}

#### ydb.auditor {#ydb-auditor}

{% include [ydb.auditor](../../_roles/ydb/auditor.md) %}

#### ydb.viewer {#ydb-viewer}

{% include [ydb.viewer](../../_roles/ydb/viewer.md) %}

#### ydb.editor {#ydb-editor}

{% include [ydb.editor](../../_roles/ydb/editor.md) %}

#### ydb.admin {#ydb-admin}

{% include [ydb.admin](../../_roles/ydb/admin.md) %}

#### ydb.kafkaApi.client {#ydb-kafkaapi-client}

{% include [ydb.kafkaApi.client](../../_roles/ydb/kafkaApi/client.md) %}


### Primitive roles {#primitive-roles}

#### {{ roles-auditor }} {#auditor}

{% include [roles-auditor](../../_roles/primitive-roles/auditor.md) %}

#### {{ roles-viewer }} {#viewer}

{% include [roles-viewer](../../_roles/primitive-roles/viewer.md) %}

#### {{ roles-editor }} {#editor}

{% include [roles-editor](../../_roles/primitive-roles/editor.md) %}

#### {{ roles-admin }} {#admin}

{% include [roles-admin](../../_roles/primitive-roles/admin.md) %}
