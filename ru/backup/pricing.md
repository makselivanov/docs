---
editable: false
---

# Правила тарификации для {{ backup-full-name }}



{% include [without-use-calculator](../_includes/pricing/without-use-calculator.md) %}

{% include [link-to-price-list](../_includes/pricing/link-to-price-list.md) %}

## Из чего складывается стоимость использования {{ backup-name }} {#rules}

Стоимость {{ backup-name }} зависит от количества защищенных виртуальных машин и суммарного объема хранилища, занятого резервными копиями.

### Защита виртуальных машин {#vms}

ВМ начинает тарифицироваться в {{ backup-name }} после того, как она была привязана к [политике резервного копирования](./concepts/policy.md). Плата продолжает взиматься, пока ВМ не будет отвязана от политики независимо от статуса ВМ.

Если вы удалите ВМ в [{{ compute-full-name }}](../compute/) с помощью [консоли управления]({{ link-console-main }}), ВМ также будет отвязана от всех политик. Если вы удалите ВМ с помощью CLI, {{ TF }} или API, она не будет автоматически отвязана от политик, сделайте это самостоятельно.

Минимальная единица тарификации — 1 ВМ в час.

### Использование хранилища {#backups}

Плата взимается за суммарный объем хранилища, занимаемый резервными копиями.

Минимальная единица тарификации — час хранения 1 МБ данных.

{% include [pricing-gb-size](../_includes/pricing-gb-size.md) %}

Если ВМ была остановлена или удалена, ее резервные копии продолжают храниться в {{ backup-name }}, и за их объем продолжает взиматься плата. На размер резервных копий влияют:
* заполненность диска ВМ;
* количество измененных данных при регулярном резервном копировании;
* возможность сжатия данных.

{% note info %}

Чтобы сократить затраты, удалите ненужные резервные копии удаленных ВМ.

{% endnote %}

Объем резервных копий ВМ может быть как меньше размера диска самой ВМ, например, при малой заполненности диска ВМ и хорошей сжимаемости данных, так и больше, например, когда резервных копий много, а данные на них постоянно меняются и плохо сжимаются.

## Цены для региона Россия {#prices}

{% include [pricing-diff-regions](../_includes/pricing-diff-regions.md) %}

Цены за месяц использования формируются из расчета 720 часов в месяц.

### Защищенные {{ backup-name }} ВМ {#prices-vms}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-vm-backups](../_pricing/backup/rub-vm-backups.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt-vm-backups](../_pricing/backup/kzt-vm-backups.md) %}

{% endlist %}



### Хранение резервных копий {#prices-backups}


{% list tabs group=pricing %}

- Цены в рублях {#prices-rub}

  {% include [rub-backup-size](../_pricing/backup/rub-backup-size.md) %}

- Цены в тенге {#prices-kzt}

  {% include [kzt-backup-size](../_pricing/backup/kzt-backup-size.md) %}

{% endlist %}



## Пример расчета стоимости {#price-example}

Пример расчета стоимости {{ backup-name }} за один месяц для следующей конфигурации:
* к политикам резервного копирования привязана 1 ВМ;
* суммарный объем резервных копий составляет 50 ГБ.


{% list tabs group=pricing %}

- Расчет в рублях {#prices-rub}

  {% include [rub-backup](../_pricing_examples/backup/rub.md) %}

- Расчет в тенге {#prices-kzt}

  {% include [kzt-backup](../_pricing_examples/backup/kzt.md) %}

{% endlist %}



