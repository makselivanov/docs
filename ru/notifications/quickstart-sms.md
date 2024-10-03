# Как начать работать с SMS в {{ cns-full-name }}

{% include [preview-stage](../_includes/notifications/preview-stage.md) %}

{% include [ask-for-turning-on](../_includes/notifications/ask-for-turning-on.md) %}

{% include [about-service](../_includes/notifications/about-service.md) %}

{% include [sms-short-description](../_includes/notifications/sms-short-description.md) %}


Чтобы начать работу с SMS:
1. [Подготовьте облако к работе](#before-you-begin).
1. [Создайте канал SMS-уведомлений c общим отправителем](#create-common-channel).
1. [Добавьте тестовый номер](#add-test-number).
1. [Отправьте тестовое SMS](#send-test-sms).
1. [Создайте канал SMS-уведомлений c индивидуальным отправителем](#create-individual-channel).
1. [Выйдите из песочницы](#quit-from-sandbox).

## Подготовьте облако к работе {#before-you-begin}

{% include [before-you-begin](../_tutorials/_tutorials_includes/before-you-begin.md) %}

## Создайте канал SMS-уведомлений c общим отправителем {#create-common-channel}

В {{ cns-name }} сообщения конечным пользователям отправляются через [каналы уведомлений](./concepts/index.md#channels).

{% include [common-sender-description](../_includes/notifications/common-sender-description.md) %}

Чтобы создать канал c общим отправителем:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором вы хотите создать канал уведомлений.
  1. В списке сервисов выберите **{{ cns-name }}**.
  1. Нажмите кнопку **Создать канал уведомлений**.
  1. На вкладке **SMS-сообщения** выберите [тип отправителя](./concepts/sms.md#senders) — **Общий отправитель** и нажмите кнопку **Создать канал**.

{% endlist %}

## Добавьте тестовый номер {#add-test-number}

{% include [sandbox-test-numbers](../_includes/notifications/sandbox-test-numbers.md) %}

Чтобы добавить тестовый номер:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Выберите канал уведомлений, созданный ранее.
  1. Перейдите на вкладку ![image](../_assets/console-icons/handset-arrow-in.svg) **Тестовые номера**.
  1. Нажмите кнопку **Добавить тестовый номер**.
  1. Во открывшемся окне введите номер телефона и нажмите кнопку **Получить код**. На указанный телефон будет отправлено SMS с кодом подтверждения.

      Поддерживаются российские телефонные номера в формате [E.164](https://ru.wikipedia.org/wiki/E.164), например `+79991112233`.

  1. Введите код из SMS и нажмите кнопку **Подтвердить**.

{% endlist %}

## Отправьте тестовое SMS {#send-test-sms}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Напротив тестового номера, добавленного ранее, нажмите ![image](../_assets/console-icons/ellipsis.svg) и выберите ![image](../_assets/console-icons/comment.svg) **Отправить сообщение**.
  1. В открывшемся окне введите текст сообщения и нажмите кнопку **Отправить**.

{% endlist %}

## Создайте канал SMS-уведомлений c индивидуальным отправителем {#create-individual-channel}

После ознакомления с функциональностью сервиса на канале с общим отправителем, вы можете зарегистрировать канал SMS-уведомлений c индивидуальным отправителем.

{% include [individual-sender-description](../_includes/notifications/individual-sender-description.md) %}

Чтобы создать канал с индивидуальным отправителем:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором вы хотите создать канал уведомлений.
  1. В списке сервисов выберите **{{ cns-name }}**.
  1. Нажмите кнопку **Создать канал уведомлений**.
  1. На вкладке **SMS-сообщения** выберите тип отправителя — **Индивидуальный отправитель**.
  1. Укажите желаемое текстовое имя отправителя и нажмите кнопку **Создать**.

{% endlist %}

После этого будет автоматически сформирована заявка в техническую поддержку на регистрацию текстового имени отправителя.

{% include [registration-duration-warning](../_includes/notifications/registration-duration-warning.md) %}

Когда регистрация завершится, вы получите ответ от технической поддержки о предоставлении доступа к каналу с индивидуальным отправителем.

## Выйдите из песочницы {#quit-from-sandbox}

{% include [individual-in-sandbox](../_includes/notifications/individual-in-sandbox.md) %}

После отработки функциональности на тестовых номерах вы можете подать заявку на выход из режима песочницы:

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Выберите канал уведомлений с индивидуальным отправителем, созданный ранее.
  1. Нажмите кнопку **Выйти из песочницы**.

      После этого будет автоматически сформирована заявка в техническую поддержку на выход из песочницы.

      {% include [sms-quota-increase](../_includes/notifications/sms-quota-increase.md) %}

{% endlist %}

После одобрения заявки вы сможете отправлять SMS на любые российские телефонные номера в формате [E.164](https://ru.wikipedia.org/wiki/E.164), например `+79991112233`.

## См. также {#see-also}

* [Как начать работать с push-уведомлениями](quickstart-sms.md)
* [Как начать работать с сервисом с помощью AWS CLI](./tools/aws-cli.md)
* [Обзор сервиса](./concepts/index.md)
* [Канал уведомлений SMS](./concepts/sms.md)
