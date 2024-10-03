---
title: "Правила тарификации для Managed Service for Elasticsearch"
description: "В этом разделе описаны правила, по которым тарифицируется использование сервиса {{ mes-name }}, и представлены актуальные цены на предоставляемые им ресурсы."
editable: false
---

# Правила тарификации для Managed Service for Elasticsearch



{% include [Elasticsearch-end-of-service](../_includes/mdb/mes/note-end-of-service.md) %}

В этом разделе описаны [правила](#rules), по которым тарифицируется использование сервиса {{ mes-name }}, и представлены [актуальные цены](#prices) на предоставляемые им ресурсы.

{% include [use-calculator](../_includes/pricing/use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

{% include [currency-choice](../_includes/pricing/currency-choice.md) %}

## Статус кластера {#running-stopped}

В зависимости от статуса кластера тарифы применяются различным образом:

* Для запущенного кластера (`Running`) тарифицируются как вычислительные ресурсы, так и объем хранилища.
* Для остановленного кластера (`Stopped`) тарифицируется только объем хранилища.

{% include [pricing-status-warning.md](../_includes/mdb/pricing-status-warning.md) %}


## Из чего складывается стоимость использования {{ mes-short-name }} {#rules}

Расчет стоимости использования {{ mes-name }} учитывает:

* используемую редакцию {{ ES }};

* вычислительные ресурсы, выделенные хостам кластера (в том числе хостам с ролью `Master node`);

* тип дисков и объем дискового пространства;

* объем исходящего трафика из {{ yandex-cloud }} в интернет.

{% include [pricing-gb-size](../_includes/pricing-gb-size.md) %}

### Использование хостов кластера {#rules-hosts-uptime}

Стоимость начисляется за каждый час работы хоста в соответствии с выделенными для него вычислительными ресурсами и используемой редакцией {{ ES }}. Поддерживаемые конфигурации ресурсов приведены в разделе [Классы хостов](concepts/instance-types.md), цены за использование vCPU и RAM — в разделе [Цены](#prices).

Вы можете выбрать класс хостов как для хостов с ролью `Data node`, так и для хостов с ролью `Master node`.

Минимальная единица тарификации — минута (например, стоимость 1,5 минут работы хоста равна стоимости 2 минут). Время, когда хост {{ ES }} не может выполнять свои основные функции, не тарифицируется.

### Использование дискового пространства {#rules-storage}

Оплачивается:

* Объем хранилища, выделенный для кластеров.

* Объем, занимаемый резервными копиями данных сверх заданного хранилища для кластера.

  * Хранение резервных копий не тарифицируется пока сумма размера данных в кластере и всех резервных копий остается меньше выбранного объема хранилища.

  * При автоматическом резервном копировании {{ mes-short-name }} не создает новую копию, а сохраняет изменения данных по сравнению с предыдущей копией. Поэтому потребление хранилища автоматическими резервными копиями растет только пропорционально объему изменений.

  * Количество хостов кластера не влияет на объем хранилища и, соответственно, на бесплатный объем резервных копий.

Цена указывается за 1 месяц использования и формируется из расчета 720 часов в месяц. Минимальная единица тарификации — 1 ГБ в минуту (например, стоимость хранения 1 ГБ в течение 1,5 минут равна стоимости хранения в течение 2 минут).

### Пример расчета стоимости кластера {#example}

Стоимость использования кластера со следующими параметрами в течение 30 дней:

* **Хосты c ролью** `Data node`: 3 хоста класса `s2.micro`: 2 × 100% vCPU, 8 ГБ RAM.
* **Хосты с ролью** `Master node`: 3 хоста класса `s2.micro`: 2 × 100% vCPU, 8 ГБ RAM.
* **Хранилище**: 100 ГБ на сетевых HDD-дисках.

Расчет стоимости:


{% list tabs group=pricing %}

- Расчет в рублях {#prices-rub}

  > 3 × (2&nbsp;×&nbsp;1,6800&nbsp;₽ + 8&nbsp;×&nbsp;2,1000&nbsp;₽) + 3 × (2&nbsp;×&nbsp;1,6800&nbsp;₽ + 8&nbsp;×&nbsp;2,1000&nbsp;₽) = 120,9600&nbsp;₽
  >
  > Итого: 120,9600&nbsp;₽ – стоимость часа работы всех хостов.

  Где:
  * 3 — количество хостов.
  * 2 — количество vCPU.
  * 1,6800&nbsp;₽ — стоимость часа использования 100% vCPU.
  * 8 — объем RAM одного хоста (в гигабайтах).
  * 2,1000&nbsp;₽ — стоимость часа использования 1 ГБ RAM на 100% vCPU.

  > 720 × 120,9600&nbsp;₽ + 100 × 3,2000&nbsp;₽ = 87&nbsp;411,2000&nbsp;₽
  >
  > Итого: 87&nbsp;411,2000&nbsp;₽ – стоимость использования кластера в течение 30 дней.

  Где:
  * 720 — количество часов в 30 днях.
  * 120,9600&nbsp;₽ — стоимость часа работы всех хостов.
  * 100 — объем хранилища на сетевых HDD-дисках (в гигабайтах).
  * 3,2000&nbsp;₽ — стоимость месяца использования 1 ГБ хранилища на сетевых HDD-дисках.

- Расчет в тенге {#prices-kzt}

  > 3 × (2&nbsp;×&nbsp;8,4000&nbsp;₸ + 8&nbsp;×&nbsp;10,5000&nbsp;₸) + 3 × (2&nbsp;×&nbsp;8,4000&nbsp;₸ + 8&nbsp;×&nbsp;10,5000&nbsp;₸) = 604,8000&nbsp;₸
  >
  > Итого: 604,8000&nbsp;₸ – стоимость часа работы всех хостов.

  Где:
  * 3 — количество хостов.
  * 2 — количество vCPU.
  * 8,4000&nbsp;₸ — стоимость часа использования 100% vCPU.
  * 8 — объем RAM одного хоста (в гигабайтах).
  * 10,5000&nbsp;₸ — стоимость часа использования 1 ГБ RAM на 100% vCPU.

  > 720 × 604,8000&nbsp;₸ + 100 × 16,0000&nbsp;₸ = 437&nbsp;056,0000&nbsp;₸
  >
  > Итого: 437&nbsp;056,0000&nbsp;₸ – стоимость использования кластера в течение 30 дней.

  Где:
  * 720 — количество часов в 30 днях.
  * 604,8000&nbsp;₸ — стоимость часа работы всех хостов.
  * 100 — объем хранилища на сетевых HDD-дисках (в гигабайтах).
  * 16,0000&nbsp;₸ — стоимость месяца использования 1 ГБ хранилища на сетевых HDD-дисках.

{% endlist %}




## Цены для региона Россия {#prices}

{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}


Все цены указаны с включением НДС.



{% include [pricing-month-term](../_includes/mdb/pricing-month-term.md) %}

{% note info %}

С 13 июня 2022 года прекращена поддержка [редакции](./concepts/es-editions.md) `Gold` в кластерах {{ mes-name }}. Создать новый кластер с этой редакцией или перейти на нее с `Basic` или `Platinum` невозможно. 6 июля 2022 года редакция всех кластеров `Gold` была автоматически повышена до `Platinum` с сохранением прежней стоимости до конца 2022 года.

{% endnote %}

### Вычислительные ресурсы хостов {#prices-hosts}

Цена вычислительных ресурсов зависит от выбранной редакции {{ ES }}:


#### Basic {#basic}

{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-hosts-basic](../_pricing/managed-elasticsearch/rub-hosts-basic.md) %}


- Цены в тенге {#prices-kzt}

  {% include [kzt-hosts-basic](../_pricing/managed-elasticsearch/kzt-hosts-basic.md) %}


{% endlist %}

#### Gold {#gold}

{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-hosts-gold](../_pricing/managed-elasticsearch/rub-hosts-gold.md) %}


- Цены в тенге {#prices-kzt}

  {% include [kzt-hosts-gold](../_pricing/managed-elasticsearch/kzt-hosts-gold.md) %}


{% endlist %}

#### Platinum {#platinum}

{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-hosts-platinum](../_pricing/managed-elasticsearch/rub-hosts-platinum.md) %}


- Цены в тенге {#prices-kzt}

  {% include [kzt-hosts-platinum](../_pricing/managed-elasticsearch/kzt-hosts-platinum.md) %}


{% endlist %}



### Хранилище {#prices-storage}

{% include [local-ssd для Ice Lake только по запросу](../_includes/ice-lake-local-ssd-note.md) %}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-storage.md](../_pricing/managed-elasticsearch/rub-storage.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt-storage.md](../_pricing/managed-elasticsearch/kzt-storage.md) %}

{% endlist %}



{% include [egress-traffic-pricing](../_includes/egress-traffic-pricing.md) %}
