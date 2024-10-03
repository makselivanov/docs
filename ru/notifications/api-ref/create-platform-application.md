# Действие CreatePlatformApplication

Создает [канал мобильных Push-уведомлений](../concepts/push.md).

## HTTP-запрос {#request}

```http
POST https://{{ cns-host }}/
```

### Query-параметры {#parameters}

Параметр | Описание
--- | ---
`Action` | **string**<br/>Обязательное поле.<br/>Параметр для обозначения типа операции.<br/>Значение: `CreatePlatformApplication`.
`Name` | **string**<br/>Обязательное поле.<br/>Имя канала уведомлений. Должно быть уникальным в [облаке](../../resource-manager/concepts/resources-hierarchy.md#cloud). Может содержать строчные и заглавные буквы латинского алфавита, цифры, подчеркивания, дефисы и точки. Допустимая длина — от 1 до 256 символов. Рекомендуется для каналов APNs указывать в имени идентификатор приложения (Bundle ID), для FCM и HMS — полное название пакета приложения (Package name).<br/>Пример: `com.example.app`.
`Platform` | **string**<br/>Обязательное поле.<br/>Платформа для отправки мобильных Push-уведомлений.<br/>Возможные значения:<ul><li>`APNS` — [Apple Push Notification service](https://developer.apple.com/notifications/).</li>`APNS_SANDBOX` — Apple Push Notification service для тестирования приложения.</li><li>`FCM` — [Firebase Cloud Messaging](https://firebase.google.com/).</li><li>`HMS` — [Huawei Mobile Services](https://developer.huawei.com/consumer/).</li></ul>
`FolderId` | **string**<br/>Обязательное поле.<br/>[Идентификатор каталога](../../resource-manager/operations/folder/get-id.md), в котором создается канал уведомлений.<br/>Пример: `b1gsm0k26v1l********`.
`Attributes.entry.N.key` | **string**<br/>Обязательное поле.<br/>Ключ [атрибута](#attributes). `N` — числовое значение.<br/>Пример: `Attributes.entry.1.key=PlatformPrincipal&Attributes.entry.2.key=PlatformCredential`.
`Attributes.entry.N.value` | **string**<br/>Обязательное поле.<br/>Значение атрибута. `N` — числовое значение.<br/>Пример: `Attributes.entry.1.value=c8gzjriSVxDDzX2fAV********&Attributes.entry.2.value=CgB6e3x9iW/qiE9l9wAUPK0e/bJQe5uIgTlYUD4bP********`.
`ResponseFormat` | **string**<br/>Формат ответа.<br/>Возможные значения:<ul><li>`XML` (по умолчанию).</li><li>`JSON`.</li></ul>

### Атрибуты {#attributes}

#### Общие атрибуты {#attributes-common}

Атрибут | Описание
--- | ---
`Description` | **string**<br/>Описание приложения.<br/>Пример: `Test application`.

#### Атрибуты APNS и APNS_SANDBOX {#attributes-apns}

Атрибут | Описание
--- | ---
`PlatformPrincipal` | **string**<br/>Идентификатор токена в формате `.p8` или SSL-сертификат в формате `.p12`. Аутентификация с токеном является предпочтительной, как более современная.
`PlatformCredential` | **string**<br/>Токен или закрытый ключ SSL-сертификата.
`ApplePlatformTeamID` | **string**<br/>Идентификатор разработчика, только при использовании токена.
`ApplePlatformBundleID` | **string**<br/>Идентификатор приложения (Bundle ID), только при использовании токена.

#### Атрибуты FCM {#attributes-fcm}

Атрибут | Описание
--- | ---
`PlatformCredential` | **string**<br/>Ключ сервисного аккаунта Google Cloud в формате JSON для аутентификации с помощью HTTP v1 API или API-ключ (server key) для аутентификации с помощью Legacy API. Версия HTTP v1 API является предпочтительной, так как с июня 2024 года Legacy API [не будет поддерживаться FCM](https://firebase.google.com/docs/cloud-messaging/migrate-v1).

#### Атрибуты HMS {#attributes-hms}

Атрибут | Описание
--- | ---
`PlatformPrincipal` | **string**<br/>Идентификатор ключа.
`PlatformCredential` | **string**<br/>API-ключ.

Подробнее об атрибутах для аутентификации см. в подразделе [Каналы мобильных Push-уведомлений](../concepts/push.md).

## Ответ {#response}

### Успешный ответ {#response-200}

При отсутствии ошибок {{ cns-name }} отвечает HTTP-кодом `200`.

Успешный ответ содержит дополнительные данные в формате XML или JSON в зависимости от указанного параметра `ResponseFormat`.

Схема данных:

{% list tabs %}

- XML

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <CreatePlatformApplicationResponse>
	  <ResponseMetadata>
		  <RequestId>string</RequestId>
	  </ResponseMetadata>
	  <CreatePlatformApplicationResult>
		  <PlatformApplicationArn>string</PlatformApplicationArn>
	  </CreatePlatformApplicationResult>
  </CreatePlatformApplicationResponse>
  ```

- JSON

  ```json
  {
    "ResponseMetadata": {
      "RequestId": "string"
    },
    "CreatePlatformApplicationResult": {
      "PlatformApplicationArn": "string"
    }
  }
  ```

{% endlist %}

Где:
* `RequestId` — идентификатор запроса.
* `PlatformApplicationArn` — идентификатор (ARN) канала уведомлений.

### Ответ с ошибкой {#response-4xx}

При возникновении ошибки {{ cns-name }} отвечает сообщением с соответствующим HTTP-кодом и дополнительным описанием в формате XML или JSON в зависимости от указанного параметра `ResponseFormat`.

Схема данных:

{% list tabs %}

- XML

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <ErrorResponseXML>
	  <RequestId>string</RequestId>
	  <Error>
		  <Code>string</Code>
		  <Message>string</Message>
	  </Error>
  </ErrorResponseXML>
  ```

- JSON

  ```json
  {
    "ErrorResponse": {
      "RequestId": "string",
      "Error": {
        "Code": "string",
        "SubCode": "string",
        "Message": "string"
      }
    }
  }
  ```

{% endlist %}

Где:
* `RequestId` — идентификатор запроса.
* `Code` — код ошибки.
* `Message` — описание ошибки.

Перечень общих кодов ошибок для всех действий см. в разделе [{#T}](common-errors.md).

Ошибки, специфичные для действия `CreatePlatformApplication`:

HTTP | Код ошибки | Описание
--- | --- | ---
400 | InvalidParameter | Канал мобильных Push-уведомлений с такими именем и платформой уже существует.
400 | InvalidParameter | Имя и платформа не могут быть использованы для создания нового канала мобильных Push-уведомлений, поскольку канал с такими же параметрами был недавно удален, данные мобильной платформы еще не обновлены.

## См. также {#see-also}

* [{#T}](index.md)
* [{#T}](send-request.md)
* [API action CreatePlatformApplication](https://docs.aws.amazon.com/sns/latest/api/API_CreatePlatformApplication.html) в документации AWS.
