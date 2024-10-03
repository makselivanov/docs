# create-query

Метод создает запрос к данным и запускает его выполнение. После этого запрос переходит в статус `RUNNING`. Результат можно [получить](get-query-results.md) только после успешного выполнения запроса. Чтобы узнать статус выполнения запроса, используйте метод [get-query-status](get-query-status.md).

{% include [!](../../_includes/api-common.md) %}

## Запрос {#request}

`POST`-запрос на адрес `/queries?project={folder_id}`, где `{folder_id}` — идентификатор каталога.

Тело запроса содержит данные в формате JSON:

```json
{
  "name": "human readable default name",
  "type": "ANALYTICS",
  "text": "string",
  "description": ""
}
```

| Поле | Описание | Допустимые значения | Примечание | Ограничения |
| ----- | ----- | ----- | ----- | ----- |
| `name` | Название запроса | | Если параметр не указан, будет присвоено название по умолчанию | Длина не должна превышать 1024 байта |
| `type` | Тип запроса | `STREAMING` — потоковый, `ANALYTICS` — аналитический | Значение по умолчанию: `ANALYTICS` | |
| `text` | Текст запроса | Строка | Обязательный | Длина должна быть от 1 до 102400 байт |
| `description` | Описание запроса | | Значение по умолчанию: пустая строка | Длина не должна превышать 10240 байт |

## Ответ {#response}

В случае успеха возвращается HTTP-ответ с кодом 200 и идентификатором запроса.

```json
{
  "id": "string"
}
```

| Поле | Описание | Примечание |
| ----- | ----- | ----- |
| `id` | Идентификатор созданного запроса | Обязательный |

## Пример {#example}

Запрос:

```json
curl \
  --request 'POST' \
  'https://api.yandex-query.cloud.yandex.net/api/fq/v1/queries?project=b1gaue5b382m********' \
  --header 'accept: application/json' \
  --header 'Content-Type: application/json' \
  --data '{
    "name": "Test query",
    "type": "ANALYTICS",
    "text": "select 1",
    "description": ""
  }'
```

Ответ:

```json
{
  "id": "csqugo80f0l3********"
}
```
