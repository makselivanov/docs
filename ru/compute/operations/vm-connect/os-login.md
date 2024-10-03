---
title: "Как подключиться к виртуальной машине по OS Login"
description: "Следуя данной инструкции, вы сможете подключиться к виртуальной машине по OS Login."
---

# Подключиться к виртуальной машине по OS Login

[OS Login](../../../organization/concepts/os-login.md) используется для предоставления пользователям и [сервисным аккаунтам](../../../iam/concepts/users/service-accounts.md) доступа к ВМ по SSH c помощью {{ iam-short-name }}.

## Перед началом работы {#before-you-begin}

{% include [cli-install](../../../_includes/cli-install.md) %}

{% include [default-catalogue](../../../_includes/default-catalogue.md) %}

При необходимости [создайте](./os-login-create-vm.md) новую виртуальную машину с поддержкой OS Login или [настройте](./enable-os-login.md) доступ по OS Login для существующей ВМ.

{% note info %}

Необходимые роли:

{% include [os-login-roles-needed-for-vm-access](../../../_includes/organization/os-login-roles-needed-for-vm-access.md) %}

{% endnote %}

## Подключиться с помощью стандартного SSH-клиента {#connect-with-ssh-client}

Подключиться к виртуальной машине с включенным доступом по OS Login можно с помощью стандартного [SSH](../../../glossary/ssh-keygen.md)-клиента как по SSH-ключу, [сохраненному](../../../organization/operations/add-ssh.md) в профиле OS Login пользователя или сервисного аккаунта, так и по короткоживущему экспортированному SSH-сертификату пользователя или сервисного аккаунта.

{% list tabs group=os_login_type %}

- По SSH-ключу {#ssh-key}

  {% include [oslogin-ssh-connect-with-ssh-key](../../../_includes/compute/oslogin-ssh-connect-with-ssh-key.md) %}

- По SSH-сертификату {#ssh-cert}

  {% include [oslogin-connect-with-exported-cert](../../../_includes/compute/oslogin-connect-with-exported-cert.md) %}

  {% include [os-login-certificate-short-lived](../../../_includes/compute/os-login-certificate-short-lived.md) %}

{% endlist %}

Произойдет подключение к указанной виртуальной машине. Если это ваше первое подключение, в операционной системе ВМ будет создан новый профиль пользователя.

## Подключиться с помощью YC CLI {#connect-with-yc-cli}

Подключиться к виртуальной машине с включенным доступом по OS Login можно с помощью [YC CLI](../../../cli/quickstart.md) как по [SSH](../../../glossary/ssh-keygen.md)-ключу, [сохраненному](../../../organization/operations/add-ssh.md) в профиле OS Login пользователя или сервисного аккаунта, так и по SSH-сертификату пользователя или сервисного аккаунта.

{% list tabs group=os_login_type %}

- По SSH-ключу {#ssh-key}

  {% include [oslogin-connect-with-key](../../../_includes/compute/oslogin-connect-with-key.md) %}

- По SSH-сертификату {#ssh-cert}

  {% include [oslogin-connect-with-cli](../../../_includes/compute/oslogin-connect-with-cli.md) %}

{% endlist %}

Произойдет подключение к указанной виртуальной машине. Если это ваше первое подключение, в операционной системе ВМ будет создан новый профиль пользователя.

#### См. также {#see-also}

* [{#T}](../../../organization/operations/os-login-access.md)
* [{#T}](../../../organization/operations/add-ssh.md)
* [{#T}](./os-login-export-certificate.md)
* [Подключиться к узлу {{ k8s }} через OS Login](../../../managed-kubernetes/operations/node-connect-oslogin.md)
* [Использовать сервисный аккаунт с профилем OS Login для управления ВМ с помощью Ansible](../../../tutorials/security/sa-oslogin-ansible.md)
