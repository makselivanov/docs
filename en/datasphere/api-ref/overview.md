---
title: "{{ ml-platform-full-name }} API overview"
description: "Managing {{ ml-platform-full-name }} resources using the API Basic methods for creating projects, uploading files, and working with notebooks"
---

# {{ ml-platform-name }} API overview

In {{ ml-platform-name }}, you can perform all basic operations on resources and notebooks using both the UI and API.

The [{{ yandex-cloud }} API](https://github.com/yandex-cloud/cloudapi) manages resources using sets of [gRPC](grpc/index.md) calls and [REST](index.md) methods. For more information about their implementation and interaction specifics, see the [{{ yandex-cloud }} API documentation](../../api-design-guide/concepts/standard-methods.md).

## Working with the community {#community}

With `CommunityService` calls and `Community` methods, you can create, update, and delete a community. You can also view a list of communities in a particular organization.

| Description | gRPC | REST |
| --- | --- | --- |
| Creates a new community in the specified organization | [Create](grpc/community_service.md#Create) | [create](Community/create.md) |
| Updates a community | [Update](grpc/community_service.md#Update) | [update](Community/update.md) |
| Deletes a community | [Delete](grpc/community_service.md#Delete) | [delete](Community/delete.md) |
| Returns information about a community | [Get](grpc/community_service.md#Get) | [get](Community/get.md) |
| Returns a list of communities in the specified organization | [List](grpc/community_service.md#List) | [list](Community/list.md) |

{% list tabs %}

- gRPC

   **Example**. Creating a community:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"organization_id": "<organization_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.CommunityService/Create
   ```

   **Example**. Viewing a list of communities in an organization:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"organization_id": "<organization_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.CommunityService/List
   ```

   For more information about the `CommunityService` calls, see the [API documentation](grpc/community_service.md).

- REST

   **Example**. Creating a community:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X POST "https://datasphere.{{ api-host }}/datasphere/v2/communities" \
     -d '{ "organizationId": "<organization_ID>" }'
   ```

   **Example**. Viewing a list of communities in an organization:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X GET "https://datasphere.{{ api-host }}/datasphere/v2/communities" \
     -d '{ "organizationId": "<organization_ID>" }'
   ```

   For more information about the `Community` methods, see the [API documentation](Community/index.md).

{% endlist %}

## Working with projects {#project}

With `ProjectService` calls and `Project` methods, you can create, open, update, or delete a project. You can also view a list of projects in a particular community.

| Description | gRPC | REST |
| --- | --- | --- |
| Creates a new project in the specified community | [Create](grpc/project_service.md#Create) | [create](Project/create.md) |
| Updates a project | [Update](grpc/project_service.md#Update) | [update](Project/update.md) |
| Deletes a project | [Delete](grpc/project_service.md#Delete) | [delete](Project/delete.md) |
| Opens a project | [Open](grpc/project_service.md#Open) | [open](Project/open.md) |
| Returns information about a project | [Get](grpc/project_service.md#Get) | [get](Project/get.md) |
| Retrieves the list of projects in the specified community | [List](grpc/project_service.md#List) | [list](Project/list.md) |

{% list tabs %}

- gRPC

   **Example**. Creating a project:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"community_id": "<community_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.ProjectService/Create
   ```

   **Example**. Viewing a list of folder projects:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"community_id": "<community_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.ProjectService/List
   ```

   For more information about the `ProjectService` calls, see the [API documentation](grpc/project_service.md).

- REST

   **Example**. Creating a project:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X POST "https://datasphere.{{ api-host }}/datasphere/v2/projects" \
     -d '{ "communityId": "<community_ID>" }'
   ```

   **Example**. Viewing a list of community projects:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X GET "https://datasphere.{{ api-host }}/datasphere/v2/projects" \
     -d '{ "communityId": "<community_ID>" }'
   ```

   For more information about the `Project` methods, see the [API documentation](Project/index.md).

{% endlist %}

## Working with notebooks {#notebook}

To run a notebook, you can use the `Execute` call or the `execute` method in `ProjectService`.

| Description | gRPC | REST |
| --- | --- | --- |
| Runs the specified notebook | [Execute](grpc/project_service.md#Execute) | [execute](Project/execute.md) |


{% list tabs %}

- gRPC

   **Example**. Running the whole notebook:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"project_id": "<project_ID>", "target": "notebook_id", "notebook_id": "<notebook_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.ProjectService/Execute
   ```

   To get the notebook ID, follow this guide: [{#T}](../operations/projects/get-notebook-cell-ids.md).

   For more information about the `ProjectService` calls, see the [API documentation](grpc/project_service.md).

- REST

   **Example**. Running the whole notebook:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X POST "https://datasphere.{{ api-host }}/datasphere/v2/projects/<project_ID>:execute" \
     -d '{ "notebook_id": "<notebook_ID>" }'
   ```

   To get the notebook ID, follow this guide: [{#T}](../operations/projects/get-notebook-cell-ids.md).

   For more information about the `Project` methods, see the [API documentation](Project/index.md).

{% endlist %}

## Working with resources {#resources}

### Resource activation and deactivation {#activate-deactivate}

Each resource has its own group of API methods implemented in {{ ml-platform-name }}. By calling the `Activate` and `Deactivate` methods of the corresponding group in a given project, you can activate and deactivate the resources as needed.

| Description | gRPC | REST |
| --- | --- | --- |
| Activates a dataset | [Activate](grpc/dataset_service.md#Activate) | [activate](Dataset/activate.md) |
| Deactivates a dataset | [Deactivate](grpc/dataset_service.md#Deactivate) | [deactivate](Dataset/deactivate.md) |
| Activates an S3 connector | [Activate](grpc/s3_service.md#Activate) | [activate](S3/activate.md) |
| Deactivates an S3 connector | [Deactivate](grpc/s3_service.md#Deactivate) | [deactivate](S3/deactivate.md) |
| Activates a Docker image | [Activate](grpc/docker_image_service.md#Activate) | [activate](DockerImage/activate.md) |

{% list tabs %}

- gRPC

   **Example**. Activating a dataset:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d "{\"project_id\": \"<project_ID>\", \"dataset_id\": \"<dataset_ID>\"}" \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.DatasetService/Activate
   ```

   **Example**. Deactivating a dataset:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d "{\"project_id\": \"<project_ID>\", \"dataset_id\": \"<dataset_ID>\"}" \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.DatasetService/Deactivate
   ```

   Learn more about the `DatasetService` calls in the [API documentation](grpc/dataset_service.md).

- REST

   **Example**. Activating a dataset:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X POST "https://datasphere.{{ api-host }}/datasphere/v2/dataset/activate" \
     -d "{ \"datasetId\": \"<dataset_ID>\", \"projectId\": \"<project_ID>\" }"
   ```

   **Example**. Deactivating a dataset:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X POST "https://datasphere.{{ api-host }}/datasphere/v2/dataset/deactivate" \
     -d "{ \"datasetId\": \"<dataset_ID>\", \"projectId\": \"<project_ID>\" }"
   ```

   Learn more about the `Dataset` methods in the [API documentation](Dataset/index.md).

{% endlist %}

### Adding and deleting resources {#add-remove}

You can use the API to add and delete resources in a project (`ProjectService`, `Project`) or community (`CommunityService`, `Community`).

To enable your project to use another project's resources, you need to [share](../operations/index.md#share) the resource in a community and add it to your project.

| Description | gRPC | REST |
| --- | --- | --- |
| Adds a resource to a community | [addResource](grpc/community_service.md#AddResource) | [addResource](Community/addResource.md) |
| Deletes a resources from a community | [removeResource](grpc/community_service.md#RemoveResource) | [removeResource](Community/removeResource.md) |
| Adds a resource to a project | [addResource](grpc/project_service.md#AddResource) | [addResource](Project/addResource.md) |
| Deletes a resource from a project | [removeResource](grpc/project_service.md#RemoveResource) | [removeResource](Project/removeResource.md) |

{% list tabs %}

- gRPC

   **Example**. Adding a resource to a project:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d "{\"project_id\": \"<project_ID>\", \"resource_id\": \"<resource_ID>\"}" \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.ProjectService/AddResource
   ```

   **Example**. Deleting a resource from a project:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d "{\"project_id\": \"<project_ID>\", \"resource_id\": \"<resource_ID>\"}" \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.ProjectService/RemoveResource
   ```

   For more information about the `ProjectService` calls, see the [API documentation](grpc/project_service.md).

- REST

   **Example**. Adding a resource to a project:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X POST "https://datasphere.{{ api-host }}/datasphere/v2/projects/<resource_ID>:addResource" \
     -d "{ \"projectId\": \"<project_ID>\" }"
   ```

   **Example**. Deleting a resource from a project:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X POST "https://datasphere.{{ api-host }}/datasphere/v2/projects/<resource_ID>:removeResource" \
     -d "{ \"projectId\": \"<project_ID>\" }"
   ```

   For more information about the `Project` methods, see the [API documentation](Project/index.md).

{% endlist %}

## Access management {#access}

You can use the API to set up access to a project (`ProjectService`, `Project`) or community (`CommunityService`, `Community`).

| Description | gRPC | REST |
| --- | --- | --- |
| Returns a list of access permissions for a project | [ListAccessBindings](grpc/project_service.md#ListAccessBindings) | [ListAccessBindings](Project/listAccessBindings.md) |
| Sets up access to a project | [SetAccessBindings](grpc/project_service.md#SetAccessBindings) | [SetAccessBindings](Project/setAccessBindings.md) |
| Updates access to a project | [UpdateAccessBindings](grpc/project_service.md#UpdateAccessBindings) | [UpdateAccessBindings](Project/updateAccessBindings.md) |
| Returns a list of access permissions for a community | [ListAccessBindings](grpc/community_service.md#ListAccessBindings) | [ListAccessBindings](Community/listAccessBindings.md) |
| Sets up access to a community | [SetAccessBindings](grpc/community_service.md#SetAccessBindings) | [SetAccessBindings](Community/setAccessBindings.md) |
| Updates access to a community | [UpdateAccessBindings](grpc/community_service.md#UpdateAccessBindings) | [UpdateAccessBindings](Community/updateAccessBindings.md) |

{% list tabs %}

- gRPC

   **Example**. Viewing a list of access permissions for a project:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"resource_id": "<project_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.ProjectService/ListAccessBindings
   ```

   **Example**. Return a list of access permissions for a community:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"resource_id": "<community_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.CommunityService/ListAccessBindings
   ```

   For more information about the [ProjectService](grpc/project_service.md) and [CommunityService](grpc/community_service.md) methods, see the API documentation.

- REST

   **Example**. Viewing a list of access permissions for a project:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X GET "https://datasphere.{{ api-host }}/datasphere/v2/projects/<resource_ID>:accessBindings"
   ```

   **Example**. Return a list of access permissions for a community:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X GET "https://datasphere.{{ api-host }}/datasphere/v2/communities/<resource_ID>:accessBindings"
   ```

   For more information about the [Project](Project/index.md) and [Community](Community/index.md) methods, see the API documentation.

{% endlist %}

## Managing consumption limits {#limits}

You can use the API to set up consumption limits for a project (`ProjectService`, `Project`).

| Description | gRPC | REST |
| --- | --- | --- |
| Returns a project's balance | [getUnitBalance](grpc/project_service.md#GetUnitBalance) | [getUnitBalance](Project/getUnitBalance.md) |
| Sets a project's balance | [setUnitBalance](grpc/project_service.md#SetUnitBalance) | [setUnitBalance](Project/setUnitBalance.md) |

{% list tabs %}

- gRPC

   **Example**. Getting a project's balance:

   ```bash
   grpcurl -rpc-header "Authorization: Bearer <IAM_token>" \
     -d '{"project_id": "<project_ID>"}' \
     datasphere.{{ api-host }}:443 \
     yandex.cloud.datasphere.v2.ProjectService/GetUnitBalance
   ```

   For more information about the `ProjectService` calls, see the [API documentation](grpc/project_service.md).

- REST

   **Example**. Getting a project's balance:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
     -X GET "https://datasphere.{{ api-host }}/datasphere/v2/projects/<project_ID>:unitBalance"
   ```

   For more information about the `Project` methods, see the [API documentation](Project/index.md).

{% endlist %}