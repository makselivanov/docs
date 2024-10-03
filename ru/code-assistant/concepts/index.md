# Обзор сервиса {{ ca-full-name }}

{% include [preview-stage](../../_includes/code-assistant/preview-stage.md) %}

{% include [ask-for-turning-on](../../_includes/code-assistant/ask-for-turning-on.md) %}

{% include [about-service](../../_includes/code-assistant/about-service.md) %}

LLM, которые применяются в {{ ca-name }}, дообучены на корпусе общедоступного кода.

Плагин {{ ca-name }} доступен для [Visual Studio Code](https://code.visualstudio.com/) и [IDE от JetBrains](https://www.jetbrains.com/ides/) версий 2024.1 и 2024.2.

{% note info %}

Для работы {{ ca-name }} требуется постоянный доступ в интернет.

{% endnote %}

Непосредственный анализ кода выполняется на вычислительных мощностях {{ yandex-cloud }}, поэтому {{ ca-name }} не нагружает локальные рабочие станции пользователей.

{% include [syntax-support](../../_includes/code-assistant/syntax-support.md) %}

{% include [auto-manual-modes](../../_includes/code-assistant/auto-manual-modes.md) %}

Поддерживаются следующие типы подсказок:
* Inline completion — автодополнения по ходу написания текста (кода, тестов, документации).
* Full-function completion — автодополнения целых функций или методов.

## См. также {#see-also}

* [{#T}](../quickstart.md)
* [Условия использования сервиса {{ ca-full-name }}](https://yandex.ru/legal/cloud_terms_code_assistant/)

