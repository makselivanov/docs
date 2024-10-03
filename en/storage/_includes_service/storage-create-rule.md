To create a rule:

1. Enable **{{ ui-key.yacloud.storage.bucket.lifecycle.field_status }}**. With this option, you can enable or disable a rule without deleting it from a configuration.
1. Fill in the fields as follows:
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_description }}**: Provide the rule description as you see fit.
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_prefix }}**: Portion of the object's [key](../concepts/object.md#key) of the required length starting from the beginning of the key. The prefix is used to sort the objects falling within the scope of the rule. If the rule is valid for all objects, leave this field empty.
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_max-size }}**: Triggers for all objects smaller than or matching the specified size.
   * **{{ ui-key.yacloud.storage.bucket.lifecycle.field_min-size }}**: Triggers for all objects larger than or matching the specified size.
1. Select and configure the types of actions to be performed with the objects when a rule triggers:
   * `{{ ui-key.yacloud.storage.bucket.lifecycle.label_expiration-type }}`: Remove any objects from bucket:

      * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_days }}`: Triggers as many days after an object was uploaded as specified in the **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** field.
      * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_date }}`: Triggers on the date specified in the **{{ ui-key.yacloud.storage.bucket.lifecycle.field_date }}** field.
      * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_expired-object-delete-marker }}`: Deletes the delete marker for which expired object versions no longer exist.

   * `{{ ui-key.yacloud.storage.bucket.lifecycle.label_transition-type }}`: Move any objects from the `STANDARD` to `COLD` storage:

      * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_days }}`: Triggers as many days after an object was uploaded as specified in the **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** field.
      * `{{ ui-key.yacloud.storage.bucket.lifecycle.value_date }}`: Triggers on the date specified in the **{{ ui-key.yacloud.storage.bucket.lifecycle.field_date }}** field.

      You can set up the change of the storage class from `STANDARD` to `ICE` or from `COLD` or `STANDARD_IA` to `ICE` using YC CLI, AWS CLI, {{ TF }}, and the API.

   * `{{ ui-key.yacloud.storage.bucket.lifecycle.label_version-expiration-type }}`: Remove non-current object versions from the bucket. Triggers as many days after an object's version became non-current as specified in the **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** field.
   * `{{ ui-key.yacloud.storage.bucket.lifecycle.label_version-transition-type }}`: Move non-current object versions from `STANDARD` to `COLD` storage. Triggers as many days after an object's version became non-current as specified in the **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** field.

      You can set up the change of the storage class from `STANDARD` to `ICE` or from `COLD` or `STANDARD_IA` to `ICE` using YC CLI, AWS CLI, {{ TF }}, and the API.

   * `{{ ui-key.yacloud.storage.bucket.lifecycle.label_abort-incomplete-multipart-upload-type }}`: Remove all parts of failed multipart uploads from the bucket. Triggers as many days after an object was uploaded as specified in the **{{ ui-key.yacloud.storage.bucket.lifecycle.field_days }}** field.

1. Click **{{ ui-key.yacloud.storage.bucket.lifecycle.button_save }}**.

You can add multiple rules at once. To add a new rule, click **{{ ui-key.yacloud.storage.bucket.lifecycle.label_add-lifecycle-settings }}** and repeat the above steps.