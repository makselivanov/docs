# Локализация текста с учетом отраслевой специфики

## Пример 1 {#example-1}

### Параметры запроса {#params}

* **Инструкция**: Переведи текст на русский язык.

* **Текст запроса**: Dr. James wants to run some additional test.

* **Температура**: `0`

* **Ответ**: Доктор Джеймс хочет провести несколько дополнительных тестов.

### Структура запроса {#structure}

```json
{
  "modelUri": "gpt://<идентификатор_каталога>/yandexgpt/latest",
  "completionOptions": {
    "stream": false,
    "temperature": 0,
    "maxTokens": "2000"
  },
  "messages": [
    {
      "role": "system",
      "text": "Переведи текст на русский язык."
    },
    {
      "role": "user",
      "text": "Dr. James wants to run some additional test."
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
                    "text": "Доктор Джеймс хочет провести несколько дополнительных тестов."
                },
                "status": "ALTERNATIVE_STATUS_FINAL"
            }
        ],
        "usage": {
            "inputTextTokens": "28",
            "completionTokens": "8",
            "totalTokens": "36"
        },
        "modelVersion": ""
    }
}
```

## Пример 2 {#example-2}

### Параметры запроса {#params}

* **Инструкция**: Переведи текст на немецкий язык.

* **Текст запроса**: Дорогой Ганс! С Днем рождения! Ура!

* **Температура**: `0`

* **Ответ**: Lieber Hans, herzlichen Glückwunsch zum Geburtstag! Alles Gute!

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
      "text": "Переведи текст на немецкий язык."
    },
    {
      "role": "user",
      "text": "Дорогой Ганс! С Днем рождения! Ура!"
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
                    "text": "Lieber Hans, herzlichen Glückwunsch zum Geburtstag! Alles Gute!"
                },
                "status": "ALTERNATIVE_STATUS_FINAL"
            }
        ],
        "usage": {
            "inputTextTokens": "29",
            "completionTokens": "24",
            "totalTokens": "53"
        },
        "modelVersion": ""
    }
}
```
