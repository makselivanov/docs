# Миграция легковесного кластера {{ dataproc-name }} в другую зону доступности

Подкластеры каждого кластера {{ dataproc-name }} находятся в одной [облачной сети](../../vpc/concepts/network.md#network) и одной [зоне доступности](../../overview/concepts/geo-scope.md). Вы можете перенести кластер в другую зону доступности. Процесс переноса зависит от типа кластера:

* Ниже описано, как перенести [легковесные кластеры](../concepts/index.md#light-weight-clusters).
* О миграции кластеров с файловой системой HDFS читайте в [руководстве](../tutorials/hdfs-cluster-migration.md).

Чтобы перенести легковесный кластер в другую зону доступности:

1. Убедитесь, что в вашей сети [настроены группы безопасности](cluster-create.md#change-security-groups).
1. [Создайте подсеть](../../vpc/operations/subnet-create.md) в зоне доступности, в которую вы переносите кластер.
1. [Настройте NAT-шлюз и привяжите таблицу маршрутизации](../../vpc/operations/create-nat-gateway.md) к новой подсети.
1. [Создайте кластер](cluster-create.md#create) в нужной зоне доступности.
1. [Удалите кластер](cluster-delete.md) в первоначальной зоне.

{% include [zone-d-host-restrictions](../../_includes/mdb/ru-central1-d-broadwell.md) %}
