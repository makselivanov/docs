---
editable: false
---

# {{ foundation-models-full-name }} pricing policy



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

In the [management console]({{ link-console-main }}), new users without a [billing account](../billing/concepts/billing-account.md) have access to models for testing:

* {{ yagpt-full-name }}: {{ gpt-freetier }} free requests per hour.
* {{ yandexart-name }}: {{ art-freetier }} free requests per day.

## What goes into the cost of using {{ foundation-models-full-name }} {#rules}

### Billing unit {#unit}

{{ foundation-models-name }} usage is detailed out in _billing units_. The cost of a billing unit is different for text [generation](./concepts/yandexgpt/index.md) and [vectorization](./concepts/embeddings.md).

### Text generation {#rules-generating}

Text generation cost is based on the overall number of [prompt](concepts/index.md#working-mode) and response [tokens](concepts/yandexgpt/tokens.md) and depends on the {{ yagpt-full-name }} request parameters. Namely, the cost depends on the following:

* [Model](concepts/yandexgpt/models.md) that gets a request.
* Model [working mode](concepts/index.md#working-mode).

The number of [prompt](concepts/index.md) and response [tokens](concepts/yandexgpt/tokens.md) for the same text may vary depending on model.

The total number of billing units is based on the overall number of prompt and response tokens and is rounded up to a whole number.

#### Tokenization {#rules-tokens}

The use of tokenizer ([TokenizerService](./text-generation/api-ref/grpc/TokenizerService.md) calls and [Tokenizer](./text-generation/api-ref/Tokenizer/index.md) methods) is not charged.

#### Fine-tuned models {#rules-tuned-generating}

The use of models fine-tuned in {{ ml-platform-full-name }} is charged according to the {{ gpt-pro }} policy.

### Text classification {#rules-text-classifier}

At the [Preview](../overview/concepts/launch-stages.md) stage, the use of classifiers based on {{ yagpt-name }} is free of charge.

### Text vectorization {#rules-embedding}

The cost of text [vectorization](./concepts/embeddings.md) (getting text embeddings) depends on the size of the text submitted for vectorization.

### Image generation {#rules-image-generation}

At the [Preview](../overview/concepts/launch-stages.md) stage, {{ yandexart-name }} is free of charge.

### Internal server errors {#error-request}

{% include [error-request](../_includes/speechkit/error-request.md) %}

## Prices for the Russia region {#prices}

{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

### Text generation in {{ yagpt-full-name }} {#pricing-generating}



{% include [usd-generating.md](../_pricing/yandexgpt/usd-generating_new.md) %}


### Text vectorization in {{ yagpt-full-name }} {#pricing-embedding}



{% include [usd-embedding.md](../_pricing/yandexgpt/usd-embedding.md) %}


## Examples of {{ yagpt-full-name }} usage cost calculation {#price-examples}

### Calculating text generation cost {#price-example-generating}

#### Example 1 {#example-generating-1}

Cost of using {{ yagpt-full-name }} for text generation with the following parameters:

* Number of prompt tokens: 225
* Number of response tokens: 525
* Model: {{ gpt-lite }}
* Model working mode: Synchronous


{% include [usd-generating-lite](../_pricing_examples/foundation-models/usd-generating-lite.md) %}

#### Example 2 {#example-generating-2}

Cost of using {{ yagpt-full-name }} for text generation with the following parameters:

* Number of prompt tokens: 115
* Number of response tokens: 1,500
* Model: {{ gpt-pro }}
* Model working mode: Asynchronous



{% include [usd-generating-pro](../_pricing_examples/foundation-models/usd-generating-pro.md) %}


#### Example 3 {#example-generating-3}

Cost of using {{ yagpt-full-name }} for text generation with the following parameters:

* Number of prompt tokens: 1,020
* Number of response tokens: 30
* Model: {{ gpt-pro }} fine-tuned in {{ ml-platform-name }}
* Model working mode: Synchronous


{% include [usd-generating-pro-ml](../_pricing_examples/foundation-models/usd-generating-pro-ml.md) %}


### Calculating text vectorization cost {#price-example-embedding}

Cost of using {{ yagpt-full-name }} for text vectorization with the following parameter:

* Number of tokens in the request: 2,000



