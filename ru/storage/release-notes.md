---
title: "История изменений в {{ objstorage-full-name }}"
description: "В разделе представлена история изменений сервиса {{ objstorage-name }}."
---

# История изменений в {{ objstorage-full-name }}

## II квартал 2024 {#q2-2024}

Улучшен дизайн консоли управления: настройки бакета теперь сгруппированы на вкладках ![image](../_assets/console-icons/wrench.svg) **{{ ui-key.yacloud.storage.bucket.switch_settings }}** и ![image](../_assets/console-icons/persons-lock.svg) **{{ ui-key.yacloud.storage.bucket.switch_security }}**.

## I квартал 2024 {#q1-2024}

* Улучшена работа с [жизненными циклами](./concepts/lifecycles.md) объектов в бакете:
  * добавлена поддержка новых фильтров для группировки объектов: [метки объекта](./concepts/tags.md#object-tags) и оператор `AND`.
  * работа с новыми фильтрами реализована в консоли управления, YC CLI, {{ TF }} и с помощью [инструментов](./tools/) с поддержкой S3 API.
* Поддержана работа с [метками бакета](./concepts/tags.md#bucket-tags) в консоли управления, YC CLI и {{ TF }}.
* Реализована возможность добавлять [группы пользователей](../organization/concepts/groups.md) {{ org-full-name }} в [список управления доступом (ACL)](./security/acl.md) и [политику доступа (bucket policy)](./security/policy.md) в консоли управления, YC CLI и {{ TF }}.
* Улучшена работа в консоли управления:
  * добавлен фильтр по префиксу в списке объектов бакета;
  * добавлена возможность скачивания объектов из списка.
