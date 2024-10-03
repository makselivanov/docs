---
title: "Как привязать облако к платежному аккаунту"
description: "Следуя данной инструкции, вы сможете привязать облако к платежному аккаунту."
---

# Привязать облако к платежному аккаунту

{% include [pin-cloud-note](../_includes/pin-cloud-note.md) %}

## Что нужно для привязки облака {#bind-roles}

Перед привязкой [облака](../../resource-manager/concepts/resources-hierarchy.md#cloud) убедитесь, что [платежный аккаунт](../concepts/billing-account.md) прошел активацию ([статус](../concepts/billing-account-statuses.md) `ACTIVE` или `TRIAL_ACTIVE`), а пользователь обладает одновременно [ролями](../../iam/concepts/access-control/roles.md):
* [resource-manager.clouds.owner](../../resource-manager/security/index.md#resource-manager-clouds-owner) на облако.
* `billing.accounts.owner` или `editor` в платежном аккаунте. Подробнее о ролях читайте в разделе [Управление доступом](../security/index.md#roles-list).

## Как привязать облако {#bind-cloud}

Чтобы привязать облако к платежному аккаунту или сменить привязку облака с одного аккаунта на другой:

{% list tabs group=instructions %}

- {{ billing-interface }} {#billing}

  1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
  1. Выберите платежный аккаунт, к которому вы ходите привязать облако.
  1. Перейдите на страницу **{{ ui-key.yacloud_billing.billing.account.switch_overview }}**.
  1. Нажмите ссылку **{{ ui-key.yacloud_billing.billing.account.dashboard-resources.button_bind-cloud }}** в блоке **{{ ui-key.yacloud_billing.billing.account.dashboard-resources.title_clouds }}**.
  1. Выберите облако из списка.
  1. Нажмите кнопку **{{ ui-key.yacloud_billing.billing.account.bind-cloud.button_bind }}** — добавленное облако появится в списке.
  1. Погасите задолженность на старом платежном аккаунте, если изменяли привязку облака.

- {{ TF }} {#tf}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  Чтобы привязать облако, у [сервисного аккаунта](../../iam/concepts/users/service-accounts.md) должна быть [назначена роль](../security/index.md#set-role) `billing.accounts.owner` или `editor` на платежный аккаунт.
  1. Опишите в конфигурационном файле параметры ресурсов, которые необходимо создать:

     ```hcl
     resource "yandex_billing_cloud_binding" "mycloud" {
       billing_account_id = "<идентификатор_платежного_аккаунта>"
       cloud_id           = "<идентификатор_облака>"
     }
     ```

     Где:
     * `billing_account_id` — идентификатор платежного аккаунта, к которому вы хотите привязать облако.
     * `cloud_id` — идентификатор облака, к которому будет привязан платежный аккаунт.

     Более подробную информацию о параметрах ресурса `yandex_billing_cloud_binding` в {{ TF }}, см. в [документации провайдера]({{ tf-provider-resources-link }}/billing_cloud_binding).
  1. Создайте ресурсы:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

  После этого облако будет привязано к платежному аккаунту. Проверить привязку облака к аккаунту можно на странице платежного аккаунта в [сервисе {{ billing-name }}]({{ link-console-billing }}).

- API {#api}

  Чтобы привязать облако, воспользуйтесь методом REST API [bindBillableObject](../api-ref/BillingAccount/bindBillableObject.md) для ресурса [BillingAccount](../api-ref/BillingAccount/index.md) или вызовом gRPC API [BillingAccountService/BindBillableObject](../api-ref/grpc/billing_account_service.md#BindBillableObject).

{% endlist %}

Если вы переносите облако из-за того, что хотите перестать использовать старый платежный аккаунт, проверьте, что в нем подключен бесплатный тарифный план «Базовый». В противном случае даже если в нем не останется привязанных облаков, начисления за платный тарифный план продолжат списываться.

{% note warning %}

Обратите внимание, что привязка облака (или другого контейнера) к [заблокированному аккаунту](../concepts/billing-account-statuses.md) приведет к остановке всех ваших ресурсов.

{% endnote %}

## Особенности управления ресурсами в организациях {#bind-cloud-organization}

1. {% include [cloud-to-pin.md](../_includes/clouds-to-pin.md) %}
1. {% include [account_scope.md](../_includes/account-scope.md) %}