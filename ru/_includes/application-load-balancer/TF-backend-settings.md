Параметры бэкенда:
* `name` — имя бэкенда.
* `port` — порт бэкенда.
* `weight` — вес бэкенда.
* `target_group_ids` — идентификатор [целевой группы](../../application-load-balancer/concepts/target-group.md). Получить список доступных целевых групп можно с помощью команды [CLI](../../cli/): `yc alb target-group list`.
* `load_balancing_config` — параметры балансировки:
  * `panic_threshold` — порог для режима паники.
* `enable_proxy_protocol` — опция, при которой балансировщик передает бэкенду метаданные своего соединения с клиентом, в том числе его IP-адрес, по [протоколу PROXY от HAProxy](https://www.haproxy.org/download/1.9/doc/proxy-protocol.txt). Если опция не задана, в бэкенд передается только IP-адрес балансировщика. Параметр доступен только для бэкендов типа `Stream`.
* `healthcheck` — параметры проверки состояния:
  * `timeout` — таймаут.
  * `interval` — интервал.
  * `healthy_threshold` — порог работоспособности.
  * `unhealthy_threshold` — порог неработоспособности.
  * `http_healthcheck` — параметры проверки состояния типа `HTTP`:
    * `path` — путь.
    * `host` — адрес хоста.
  * `grpc_healthcheck` — параметры проверки состояния типа `gRPC`:
    * `service_name` — имя проверяемого gRPC-сервиса. Если сервис не указан, проверяется общее состояние бэкенда.
  * `stream_healthcheck` — параметры проверки состояния типа `Stream`:
    * `send` — данные, которые отправляются на эндпоинт для проверки состояния.
    * `receive` — данные, которые должны поступить с эндпоинта, чтобы он прошел проверку состояния.

  {% include [backend-healthcheck](./backend-healthcheck.md) %}