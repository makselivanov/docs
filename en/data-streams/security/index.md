# Access management in {{ yds-name }}

{{ yds-name }} uses [roles](../../iam/concepts/access-control/roles.md) to manage access rights.

{{ yandex-cloud }} users can only perform operations on resources that are allowed by the roles assigned to them. As long as a user has no roles assigned, almost all operations are forbidden.

To enable access to {{ yds-full-name }} resources (data streams, {{ ydb-full-name }} databases storing the data streams, and database users), assign the required roles from the list below to a Yandex account, [service account](../../iam/concepts/users/service-accounts.md), [federated users](../../iam/concepts/federations.md), [user group](../../organization/operations/manage-groups.md), [system group](../../iam/concepts/access-control/system-group.md), or [public group](../../iam/concepts/access-control/public-group.md). Currently, a role can only be assigned for a parent resource (folder or cloud). Roles are inherited by nested resources.

Roles for a resource can be assigned by users who have the `yds.admin` role or one of the following roles for that resource:

{% include [roles-list](../../_includes/iam/roles-list.md) %}

{% note info %}

For more information about role inheritance, see [{#T}](../../resource-manager/concepts/resources-hierarchy.md#access-rights-inheritance) in the {{ resmgr-full-name }} documentation.

{% endnote %}

## Assigning roles {#grant-roles}

To assign a user a role:

{% include [grant-role-console](../../_includes/grant-role-console.md) %}

## Which roles exist in the service {#roles-list}

The list below shows all roles considered when verifying access permissions in {{ yds-name }}.

### Service roles {#service-roles}

#### yds.viewer {#yds-viewer}

{% include [yds.viewer](../../_roles/yds/viewer.md) %}

#### yds.writer {#yds-writer}

{% include [yds.writer](../../_roles/yds/writer.md) %}

#### yds.editor {#yds-editor}

{% include [yds.editor](../../_roles/yds/editor.md) %}

#### yds.admin {#yds-admin}

{% include [yds.admin](../../_roles/yds/admin.md) %}

### Primitive roles {#primitive-roles}

#### {{ roles-viewer }} {#viewer}

A user with the `{{ roles-viewer }}` role can view information about resources, e.g., lists of data streams and databases they are created in, their properties.

#### {{ roles-editor }} {#editor}

A user with the `{{ roles-editor }}` role can manage any resources, e.g., create a stream or delete it. In addition, this role allows writing application data to streams.

The `{{ roles-editor }}` role also includes all permissions of the `{{ roles-viewer }}` role.

#### {{ roles-admin }} {#admin}

Users with the `{{ roles-admin }}` role can manage resource access rights, for example, allow other users to create streams or view information about them.

The `{{ roles-admin }}` role also includes all permissions of the `{{ roles-editor }}` role.