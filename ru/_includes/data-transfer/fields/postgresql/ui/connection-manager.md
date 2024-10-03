**{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnection.authorization_connman.title }}** — позволяет использовать подключение к базе данных через [{{ connection-manager-full-name }}](../../../../../metadata-hub/quickstart/connection-manager.md):

* **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.ConnmanConnection.connection_id.title }}** — укажите идентификатор подключения из {{ connection-manager-name }}.

* {% include [database](../../../fields/postgresql/ui/database.md) %}

{% note warning %}

Чтобы использовать подключение из {{ connection-manager-name }}, у пользователя должны быть [права доступа](../../../../../metadata-hub/operations/connection-access.md) не ниже `connection-manager.user` к этому подключению.

{% endnote %}
