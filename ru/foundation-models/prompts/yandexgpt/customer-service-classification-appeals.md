# Классификация обращений

## Пример 1 {#example-1}

### Параметры запроса {#params}

* **Инструкция**: Классифицируй обращения клиента в подходящую категорию. Категории: Статус заказа, Возврат и обмен товаров, Характеристики продукта, Технические проблемы, Другое. В ответе укажи только категорию.

* **Текст запроса**: При оформлении заказа на вашем сайте возникла проблема — не могу добавить товар в корзину. Можете помочь мне разобраться?

* **Температура**: `0`

* **Ответ**: Технические проблемы

### Структура запроса {#structure}

```json
{
  "modelUri": "gpt://<идентификатор_каталога>/yandexgpt-lite",
  "completionOptions": {
    "stream": false,
    "temperature": 0,
    "maxTokens": "2000"
  },
  "messages": [
    {
      "role": "system",
      "text": "Классифицируй обращения клиента в подходящую категорию. Категории: Статус заказа, Возврат и обмен товаров, Характеристики продукта, Технические проблемы, Другое. В ответе укажи только категорию."
    },
    {
      "role": "user",
      "text": "При оформлении заказа на вашем сайте возникла проблема — не могу добавить товар в корзину. Можете помочь мне разобраться?"
    }
  ]
}
```

{% include [folder-id](../../../_includes/foundation-models/yandexgpt/folder-id.md) %}

{% list tabs group=programming_language %}

- cURL {#curl}

  ```bash
  curl \
    --insecure \
    --verbose \
    --request POST \
    --header "Authorization: Bearer <IAM-токен>" \
    --data @prompt.json \
    https://llm.{{ api-host }}/foundationModels/v1/completion
  ```

  Где:

  * `<IAM-токен>` — значение IAM-токена, полученного для вашего аккаунта.
  * `prompt.json` — файл в формате JSON, содержащий параметры запроса.

{% endlist %}

### Ответ {#answer}

```json
{
    "result": {
        "alternatives": [
            {
                "message": {
                    "role": "assistant",
                    "text": "Технические проблемы"
                },
                "status": "ALTERNATIVE_STATUS_FINAL"
            }
        ],
        "usage": {
            "inputTextTokens": "67",
            "completionTokens": "2",
            "totalTokens": "69"
        },
        "modelVersion": ""
    }
}
```

## Пример 2 {#example-2}

### Параметры запроса {#params}

* **Инструкция**: Классифицируй обращения клиента в подходящую категорию. Категории: Статус заказа, Возврат и обмен товаров, Характеристики продукта, Технические проблемы, Другое. В ответе укажи только категорию.

* **Текст запроса**: Здравствуйте, я недавно получил ваш товар, но он не подошел мне по размеру. Как я могу вернуть его и обменять на другой размер?

* **Температура**: `0`

* **Ответ**: Возврат и обмен товаров

### Структура запроса {#structure}

```json
{
  "modelUri": "gpt://<идентификатор_каталога>/yandexgpt-lite",
  "completionOptions": {
    "stream": false,
    "temperature": 0,
    "maxTokens": "2000"
  },
  "messages": [
    {
      "role": "system",
      "text": "Классифицируй обращения клиента в подходящую категорию. Категории: Статус заказа, Возврат и обмен товаров, Характеристики продукта, Технические проблемы, Другое. В ответе укажи только категорию."
    },
    {
      "role": "user",
      "text": "Здравствуйте, я недавно получил ваш товар, но он не подошел мне по размеру. Как я могу вернуть его и обменять на другой размер?"
    }
  ]
}
```

{% include [folder-id](../../../_includes/foundation-models/yandexgpt/folder-id.md) %}

{% list tabs group=programming_language %}

- cURL {#curl}

  ```bash
  curl \
    --insecure \
    --verbose \
    --request POST \
    --header "Authorization: Bearer <IAM-токен>" \
    --data @prompt.json \
    https://llm.{{ api-host }}/foundationModels/v1/completion
  ```

  Где:

  * `<IAM-токен>` — значение IAM-токена, полученного для вашего аккаунта.
  * `prompt.json` — файл в формате JSON, содержащий параметры запроса.

{% endlist %}

### Ответ {#answer}

```json
{
    "result": {
        "alternatives": [
            {
                "message": {
                    "role": "assistant",
                    "text": "Возврат и обмен товаров"
                },
                "status": "ALTERNATIVE_STATUS_FINAL"
            }
        ],
        "usage": {
            "inputTextTokens": "73",
            "completionTokens": "4",
            "totalTokens": "77"
        },
        "modelVersion": ""
    }
}
```
