# Providing secure access to content in {{ cdn-name }}

In this tutorial, you will create a website to generate signed links with a [secure token](../../cdn/concepts/secure-tokens.md) to a CDN resource in {{ cdn-name }}. The referenced content will be available only for five minutes from the time the link was received and only to the user the link was generated for. 

The website will be deployed on a [VM](../../compute/concepts/vm.md) created from a public [LAMP](/marketplace/products/yc/lamp) image with a pre-installed [Apache HTTP server](https://httpd.apache.org/). The website will be available via the domain name delegated in {{ dns-full-name }} for which a TLS certificate will be issued in {{ certificate-manager-full-name }}.

To configure secure access to content in {{ cdn-name }}:

1. [Prepare your cloud](#before-you-begin).
1. [Create a VM with a web server](#create-web-server).
1. [Create and configure a public DNS zone](#configure-dns).
1. [Add a TLS certificate to {{ certificate-manager-name }}](#issue-certificate).
1. [Prepare a source bucket for the CDN resource](#setup-bucket-origin).
1. [Create a CDN resource](#setup-cdn-resource).
1. [Create a CNAME resource record for the CDN resource](#create-cdn-cname-record).
1. [Publish the webiste on the web server.](#publish-website).
1. [Test secure access to files](#check).

If you no longer need the resources you created, [delete them](#clear-out).


## Prepare your cloud {#before-you-begin}

{% include [before-you-begin](../../_tutorials/_tutorials_includes/before-you-begin.md) %}


### Required paid resources {#paid-resources}

The support cost includes:

* Fee for using a public IP address (see [{{ vpc-full-name }}](../../vpc/pricing.md) pricing).
* Fee for VM computing resources and disks (see [{{ compute-full-name }}](../../compute/pricing.md) pricing).
* Fee for using a public DNS zone and public DNS requests (see [{{ dns-full-name }} pricing](../../dns/pricing.md)).
* Fee for data storage in {{ objstorage-name }}, operations with data, and outgoing traffic (see [{{ objstorage-name }}](../../storage/pricing.md) pricing).
* Fee for outgoing traffic from CDN servers (see [{{ cdn-name }} pricing](../../cdn/pricing.md)).


### Create a cloud network and subnet {#create-network}

{% list tabs group=instructions %}

- Management console {#console} 

  1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
  1. At the top right, click **{{ ui-key.yacloud.vpc.networks.button_create }}**.
  1. In the **{{ ui-key.yacloud.vpc.networks.create.field_name }}** field, specify `webserver-network`.
  1. In the **{{ ui-key.yacloud.vpc.networks.create.field_advanced }}** field, disable the **{{ ui-key.yacloud.vpc.networks.create.field_is-default }}** option.
  1. Click **{{ ui-key.yacloud.vpc.networks.button_create }}**.
  1. In the left-hand panel, select ![subnets](../../_assets/console-icons/nodes-right.svg) **{{ ui-key.yacloud.vpc.switch_networks }}**.
  1. At the top right, click **{{ ui-key.yacloud.vpc.subnetworks.button_action-create }}**.
  1. In the **{{ ui-key.yacloud.vpc.subnetworks.create.field_name }}** field, specify `webserver-subnet-{{ region-id }}-b`.
  1. In the **{{ ui-key.yacloud.vpc.subnetworks.create.field_zone }}** field, select the `{{ region-id }}-b` availability zone.
  1. In the **{{ ui-key.yacloud.vpc.subnetworks.create.field_network }}** field, select the `webserver-network` cloud network.
  1. In the **{{ ui-key.yacloud.vpc.subnetworks.create.field_ip }}** field, specify `192.168.1.0/24`.
  1. Click **{{ ui-key.yacloud.vpc.subnetworks.create.button_create }}**.

- {{ yandex-cloud }} CLI {#cli}

  1. Create a network named `webserver-network`:

      ```bash
      yc vpc network create webserver-network
      ```

      Result:

      ```
      id: enp1gg8kr3pv********
      folder_id: b1gt6g8ht345********
      created_at: "2023-12-20T20:08:11Z"
      name: webserver-network
      default_security_group_id: enppne4l2eg5********
      ```

      For more information about the `yc vpc network create` command, see the [CLI reference](../../cli/cli-ref/managed-services/vpc/network/create.md).

  1. Create a subnet in the `{{ region-id }}-b` availability zone:

      ```bash
      yc vpc subnet create webserver-subnet-{{ region-id }}-b \
        --zone {{ region-id }}-b \
        --network-name webserver-network \
        --range 192.168.1.0/24
      ```

      Result:

      ``` 
      id: e2li9tcgi7ii********
      folder_id: b1gt6g8ht345********
      created_at: "2023-12-20T20:11:16Z"
      name: webserver-subnet-{{ region-id }}-b
      network_id: enp1gg8kr3pv********
      zone_id: {{ region-id }}-b
      v4_cidr_blocks:
        - 192.168.1.0/24
      ```

      For more information about the `yc vpc subnet create` command, see the [CLI reference](../../cli/cli-ref/managed-services/vpc/subnet/create.md).

- API {#api}

  1. To create a network, use the [create](../../vpc/api-ref/Network/create.md) REST API method for the [Network](../../vpc/api-ref/Network/index.md) resource or the [NetworkService/Create](../../vpc/api-ref/grpc/network_service.md#Create) gRPC API call.

  1. To create a subnet, use the [create](../../vpc/api-ref/Subnet/create.md) REST API method for the [Subnet](../../vpc/api-ref/Subnet/index.md) resource or the [SubnetService/Create](../../vpc/api-ref/grpc/subnet_service.md#Create) gRPC API call.

{% endlist %}


### Create a security group {#create-sg}

Create a [security group](../../vpc/concepts/security-groups.md) that allows inbound TCP traffic on ports `22`, `80`, and `443` as well as any outbound traffic.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/shield.svg) **{{ ui-key.yacloud.vpc.switch_security-groups }}**. 
  1. Click **{{ ui-key.yacloud.vpc.network.security-groups.button_create }}**.
  1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-name }}** field, specify the name: `webserver-sg`.
  1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-network }}** field, select the `webserver-network` network you created earlier.
  1. Under **{{ ui-key.yacloud.vpc.network.security-groups.forms.label_section-rules }}**, [create](../../vpc/operations/security-group-add-rule.md) the following traffic management rules:

      | Traffic<br/>routing | {{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-description }} | {{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }} | {{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }} | {{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }} /<br/>{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }} | {{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }} |
      | --- | --- | --- | --- | --- | --- |
      | Incoming | `http`           | `80` | `TCP` | `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}` | `0.0.0.0/0` |
      | Incoming | `https`            | `443`   | `TCP`  | `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}` | `0.0.0.0/0` |
      | Incoming | `ssh`            | `22`   | `TCP`  | `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}` | `0.0.0.0/0` |
      | Outgoing | `any`           | `All` | `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}` | `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}` | `0.0.0.0/0` |

  1. Click **{{ ui-key.yacloud.common.save }}**.

- {{ yandex-cloud }} CLI {#cli}
  
  Run this command:

  ```bash
  yc vpc security-group create \
    --name webserver-sg \
    --rule "description=http,direction=ingress,port=80,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
    --rule "description=https,direction=ingress,port=443,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
    --rule "description=ssh,direction=ingress,port=22,protocol=tcp,v4-cidrs=[0.0.0.0/0]" \
    --rule "description=any,direction=egress,port=any,protocol=any,v4-cidrs=[0.0.0.0/0]" \
    --network-name webserver-network
  ```

  Result:

  ```yaml
  id: enp4htsnl1sa********
  folder_id: b1gt6g8ht345********
  created_at: "2023-12-23T19:07:03Z"
  name: webserver-sg
  network_id: enp37qpnksl2********
  status: ACTIVE
  rules:
    - id: enpdu0t8san9********
      description: http
      direction: INGRESS
      ports:
        from_port: "80"
        to_port: "80"
      protocol_name: TCP
      protocol_number: "6"
      cidr_blocks:
        v4_cidr_blocks:
          - 0.0.0.0/0
    - id: enpr7oirpff5********
      description: https
      direction: INGRESS
      ports:
        from_port: "443"
        to_port: "443"
      protocol_name: TCP
      protocol_number: "6"
      cidr_blocks:
        v4_cidr_blocks:
          - 0.0.0.0/0
    - id: enp0bgk6dkdd********
      description: ssh
      direction: INGRESS
      ports:
        from_port: "22"
        to_port: "22"
      protocol_name: TCP
      protocol_number: "6"
      cidr_blocks:
        v4_cidr_blocks:
          - 0.0.0.0/0
    - id: enpspns0tfml********
      description: any
      direction: EGRESS
      protocol_name: ANY
      protocol_number: "-1"
      cidr_blocks:
        v4_cidr_blocks:
          - 0.0.0.0/0
  ```

  For more information about the `yc vpc security-group create` command, see the [CLI reference](../../cli/cli-ref/managed-services/vpc/security-group/create.md).

  Save the obtained security group ID (`id`): you will need it to create a VM.

- API {#api}

  To create a security group, use the [create](../../vpc/api-ref/SecurityGroup/create.md) REST API method for the [SecurityGroup](../../vpc/api-ref/SecurityGroup/index.md) resource or the [SecurityGroupService/Create](../../vpc/api-ref/grpc/security_group_service.md#Create) gRPC API call.

{% endlist %}


### Prepare files for uploading to the bucket {#prepare-files}

1. Save any image in JPEG format to the `content.jpg` file.

1. Create a file named `index.html`:

    ```html
    <html>
        <body>
        </body>
    </html>
    ```


## Create a VM with a web server {#create-web-server}

Before you start, prepare a [key pair](../../compute/operations/vm-connect/ssh.md#creating-ssh-keys) (public and private keys) to access your VM over SSH.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
  1. In the left-hand panel, select ![image](../../_assets/console-icons/server.svg) **{{ ui-key.yacloud.compute.switch_instances }}**.
  1. Click **{{ ui-key.yacloud.compute.instances.button_create }}**.
  1. Under **{{ ui-key.yacloud.compute.instances.create.section_base }}**:

      * In the **{{ ui-key.yacloud.common.name }}** field, specify the name: `mywebserver`.
      * In the **{{ ui-key.yacloud.compute.instances.create.field_zone }}** field, select `{{ region-id }}-b`.

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_image }}**, go to the **{{ ui-key.yacloud.compute.instances.create.image_value_marketplace }}** tab and click **{{ ui-key.yacloud.compute.instances.create.button_show-all-marketplace-products }}**.
  1. In the window that opens, find and select [LAMP](/marketplace/products/yc/lamp).
  1. Under **{{ ui-key.yacloud.compute.instances.create.section_network }}**:

      * In the **{{ ui-key.yacloud.component.compute.network-select.field_subnetwork }}** field, select the `webserver-subnet-{{ region-id }}-b` subnet you created earlier.
      * In the **{{ ui-key.yacloud.component.compute.network-select.field_external }}** field, select `{{ ui-key.yacloud.component.compute.network-select.switch_auto }}`.
      * In the **{{ ui-key.yacloud.component.compute.network-select.field_security-groups }}** field, select the `webserver-sg` security group you created earlier.

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_access }}**, specify the information required to access the VM:

      * Enter the username in the **{{ ui-key.yacloud.compute.instances.create.field_user }}** field: `yc-user`.
      * In the **{{ ui-key.yacloud.compute.instances.create.field_key }}** field, paste the contents of the [public key](../../compute/operations/vm-connect/ssh.md#creating-ssh-keys) created earlier.

  1. Click **{{ ui-key.yacloud.compute.instances.create.button_create }}**.

- {{ yandex-cloud }} CLI {#cli}

  Run this command:

  ```bash
  yc compute instance create \
    --name mywebserver \
    --zone {{ region-id }}-b \
    --network-interface subnet-name=webserver-subnet-{{ region-id }}-b,nat-ip-version=ipv4,security-group-ids=<security_group_ID> \
    --create-boot-disk image-folder-id=standard-images,image-id=fd8jtn9i7e9ha5q25niu \
    --ssh-key ~/.ssh/id_ed25519.pub
  ```

  Where:
  * `<security_group_ID>`: Previously saved security group ID.
  * `--ssh-key`: Path to the file with the public SSH key. e.g., `~/.ssh/id_ed25519.pub`.

  Result:

  ```yml

  done (32s)
  id: fhmaq4shfrcm********
  folder_id: b1gt6g8ht345********
  created_at: "2023-12-23T05:36:34Z"
  name: mywebserver
  zone_id: {{ region-id }}-b
  platform_id: standard-v2
  resources:
    memory: "2147483648"
    cores: "2"
    core_fraction: "100"
  status: RUNNING
  metadata_options:
    gce_http_endpoint: ENABLED
    aws_v1_http_endpoint: ENABLED
    gce_http_token: ENABLED
    aws_v1_http_token: DISABLED
  boot_disk:
    mode: READ_WRITE
    device_name: fhmprher1d0q********
    auto_delete: true
    disk_id: fhmprher1d0q********
  network_interfaces:
    - index: "0"
      mac_address: d0:0d:ad:13:91:7e
      subnet_id: e9bk1m87r4m4********
      primary_v4_address:
        address: 192.168.1.11
        one_to_one_nat:
          address: 158.160.***.***
          ip_version: IPV4
      security_group_ids:
        - enpa5j0mrgm4********
  gpu_settings: {}
  fqdn: fhmaq4shfrcm********.auto.internal
  scheduling_policy: {}
  network_settings:
    type: STANDARD
  placement_policy: {}
  ```

  For more information about the `yc compute instance create` command, see the [CLI reference](../../cli/cli-ref/managed-services/compute/instance/create.md).

- API {#api}

  To create a VM, use the [create](../../compute/api-ref/Instance/create.md) REST API method for the [Instance](../../compute/api-ref/Instance/) resource or the [InstanceService/Create](../../compute/api-ref/grpc/instance_service.md#Create) gRPC API call.

{% endlist %}

This will create the `mywebserver` VM in your folder. To [connect](../../compute/operations/vm-connect/ssh.md#vm-connect) to the VM over SSH, use the `yc-user` username and the VM’s [public IP address](../../vpc/concepts/address.md#public-addresses). If you plan to use the created web server over a long period of time, [make](../../vpc/operations/set-static-ip.md) this VM's public IP address static.


## Create and configure a public DNS zone {#configure-dns}

1. Create a public [DNS zone](../../dns/concepts/dns-zone.md) in {{ dns-full-name }}.

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
      1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_dns }}**.
      1. Click **{{ ui-key.yacloud.dns.button_zone-create }}**.
      1. Specify the zone settings consistent with your domain:

         1. **{{ ui-key.yacloud.dns.label_zone }}**: Domain zone. The zone name must end with a period. For example, the `example.com.` zone name corresponds to the `example.com` domain. To create a domain zone with non-Latin characters, use the [Punycode](https://{{ lang }}.wikipedia.org/wiki/Punycode) encoding.
         1. **{{ ui-key.yacloud.common.type }}**: `{{ ui-key.yacloud.dns.label_public }}`.
         1. **{{ ui-key.yacloud.common.name }}**: `my-domain-zone`.

      1. Click **{{ ui-key.yacloud.common.create }}**.

    - {{ yandex-cloud }} CLI {#cli}

      Run this command:

      ```bash
      yc dns zone create \
        --name my-domain-zone \
        --zone <domain_name> \
        --public-visibility
      ```

      Where `--zone` is your domain name, e.g., `example.com.`. The `--zone` parameter value must end with a period. For example, the `example.com.` zone name corresponds to the `example.com` domain.

      Result:

      ```
      id: dns39gihj0ef********
      folder_id: b1gt6g8ht345********
      created_at: "2023-12-21T16:43:37.883Z"
      name: my-domain-zone
      zone: example.com.
      public_visibility: {}
      ```

      For more information about the `yc dns zone create` command, see the [CLI reference](../../cli/cli-ref/managed-services/dns/zone/create.md).

    - API {#api}

      To create a public DNS zone, use the [create](../../dns/api-ref/DnsZone/create.md) REST API method for the [DnsZone](../../dns/api-ref/DnsZone/index.md) resource or the [DnsZoneService/Create](../../dns/api-ref/grpc/dns_zone_service.md#Create) gRPC API call.

    {% endlist %}

1. Delegate your domain to {{ dns-name }}. To do this, in your domain registrar account, specify these DNS server addresses in the domain settings: `ns1.{{ dns-ns-host-sld }}` and `ns2.{{ dns-ns-host-sld }}`.

1. In your DNS zone, create an A resource record pointing to the public IP address of the previously created VM with a web server:

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
      1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_dns }}**.
      1. Select the previously created DNS zone.
      1. Click **{{ ui-key.yacloud.dns.button_record-set-create }}**.
      1. Set the record parameters:
         1. In the **{{ ui-key.yacloud.common.name }}** field, select `{{ ui-key.yacloud.dns.label_fqdn-equal-to-zone }}`.
         1. In the **{{ ui-key.yacloud.common.type }}** field, select `A` as the [record type](../../dns/concepts/resource-record.md#rr-types).
         1. In the **{{ ui-key.yacloud.dns.label_records }}** field, specify the [public IP address](../../vpc/concepts/address.md#public-addresses) of the previously created VM with a web server.

             You can find the VM IP address in the **{{ ui-key.yacloud.compute.instance.overview.section_network }}** section on the VM page in the [management console]({{ link-console-main }}) or using this CLI command: `yc compute instance get mywebserver`.

      1. Click **{{ ui-key.yacloud.common.create }}**.

    - {{ yandex-cloud }} CLI {#cli}

      Run this command:

      ```bash
      yc dns zone add-records \
        --name my-domain-zone \
        --record "@ 600 A <VM_IP_address>"
      ```

      Where `<VM_IP_address>` is the [public IP address](../../vpc/concepts/address.md#public-addresses) of the previously created VM with a web server.

      You can find the VM IP address in the **{{ ui-key.yacloud.compute.instance.overview.section_network }}** section on the VM page in the [management console]({{ link-console-main }}) or using this CLI command: `yc compute instance get mywebserver`.

      Result:

      ```text
      +--------+--------------+------+---------------+-----+
      | ACTION |     NAME     | TYPE |     DATA      | TTL |
      +--------+--------------+------+---------------+-----+
      | +      | example.com. | A    | 51.250.**.*** | 600 |
      +--------+--------------+------+---------------+-----+
      ```

      For more information about the `yc dns zone add-records` command, see the [CLI reference](../../cli/cli-ref/managed-services/dns/zone/add-records.md).

    - API {#api}

      To create a resource record in a DNS zone, use the [updateRecordSets](../../dns/api-ref/DnsZone/updateRecordSets.md) REST API method for the [DnsZone](../../dns/api-ref/DnsZone/index.md) resource or the [DnsZoneService/UpdateRecordSets](../../dns/api-ref/grpc/dns_zone_service.md#UpdateRecordSets) gRPC API call.

    {% endlist %}


## Add a TLS certificate to {{ certificate-manager-name }} {{ certificate-manager-full-name }}

1. Add a Let's Encrypt® [certificate](../../certificate-manager/concepts/managed-certificate.md) to {{ certificate-manager-name }} for your domains that the web server and CDN resource will use.

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
      1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_certificate-manager }}**.
      1. Click **{{ ui-key.yacloud.certificate-manager.button_empty-action }}** and select **{{ ui-key.yacloud.certificate-manager.action_request }}**.
      1. In the window that opens, specify `mymanagedcert` in the **{{ ui-key.yacloud.certificate-manager.metadata.field_name }}** field.
      1. In the **{{ ui-key.yacloud.certificate-manager.request.field_domains }}** field, specify your domain name, e.g., `example.com`.

          In the same field, in a new line, specify the name of the subdomain to be used for the CDN resource, e.g., `cdn.example.com`.
      1. Select the [permission check type](../../certificate-manager/concepts/challenges.md) for the `{{ ui-key.yacloud.certificate-manager.request.challenge-type_label_dns }}` domain.
      1. Click **{{ ui-key.yacloud.certificate-manager.request.button_request }}**.

    - {{ yandex-cloud }} CLI {#cli}

      Run this command:

      ```bash
      yc certificate-manager certificate request \
        --name mymanagedcert \
        --challenge dns \
        --domains <domain_name>,<subdomain_name>
      ```

      Where:
      * `<domain_name>`: Name of your domain for the web server, e.g., `example.com`.
      * `<subdomain_name>`: Name of the subdomain that the CDN resource will use, e.g., `cdn.example.com`.

      Result:
      ```bash
      id: fpqbs12t6ion********
      folder_id: b1gt6g8ht345********
      created_at: "2023-12-24T14:36:39.299844798Z"
      name: mymanagedcert
      type: MANAGED
      domains:
        - example.com
        - cdn.example.com
      status: VALIDATING
      updated_at: "2023-12-24T14:36:39.299844798Z"
      ```

      For more information about the `yc certificate-manager certificate request` command, see the [CLI reference](../../cli/cli-ref/managed-services/certificate-manager/certificate/request.md).

      Save the ID (`id`) of the created certificate as you will need it when creating a CDN resource.

    - API {#api}

      To add a certificate, use the [requestNew](../../certificate-manager/api-ref/Certificate/requestNew.md) REST API method for the [Certificate](../../certificate-manager/api-ref/Certificate/) resource or the [CertificateService/RequestNew](../../certificate-manager/api-ref/grpc/certificate_service.md#RequestNew) gRPC API call.

    {% endlist %}

    The new certificate will appear in the certificate list with the `Validating` status. This status means that a Let's Encrypt® certificate was requested and you need to pass a [domain permission check](../../certificate-manager/operations/managed/cert-validate.md) for it to be successfully processed.

1. To successfully issue the certificate, pass the domain permission check:

    {% list tabs group=instructions %}

    - Management console {#console}

        1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
        1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_certificate-manager }}**.
        1. In the list of certificates, select `mymanagedcert`.
        1. In the window that opens, under **{{ ui-key.yacloud.certificate-manager.overview.section_challenges }}**, select `CNAME record`.
        1. In the section of the first domain, click **{{ ui-key.yacloud.component.dns-integration.button_add-domain }}** and then click **{{ ui-key.yacloud.common.create }}** in the window that opens.
        1. Repeat the above action for the second domain.

        The domain permission check may take from a few minutes to a few days. Wait until it is complete. As the result, the certificate will be issued and get the `Issued` status.

    - {{ yandex-cloud }} CLI {#cli}

        1. Get values of the resource records required for passing the check:

            ```bash
            yc certificate-manager certificate get \
              --name mymanagedcert \
              --full
            ```

            Result:

            ```bash
            id: fpq2gpi42teg********
            folder_id: b1gt6g8ht345********
            created_at: "2023-12-24T18:13:45.960Z"
            name: mymanagedcert
            type: MANAGED
            domains:
              - example.com
              - cdn.example.com
            status: VALIDATING
            updated_at: "2023-12-24T18:13:45.960Z"
            challenges:
              - domain: cdn.example.com
                type: DNS
                created_at: "2023-12-24T18:13:45.960Z"
                updated_at: "2023-12-24T18:13:49.280Z"
                status: PENDING
                message: Create a record in your DNS provider.
                dns_challenge:
                  name: _acme-challenge.cdn.example.com.
                  type: CNAME
                  value: fpq2gpi42teg********.cm.yandexcloud.net.
              - domain: cdn.example.com
                type: DNS
                created_at: "2023-12-24T18:13:45.960Z"
                updated_at: "2023-12-24T18:13:49.280Z"
                status: PENDING
                message: Create a record in your DNS provider.
                dns_challenge:
                  name: _acme-challenge.cdn.example.com.
                  type: TXT
                  value: d9RzZH8WZucSY8mXs9cEg1wNteaaNqbxZK7********
              - domain: example.com
                type: DNS
                created_at: "2023-12-24T18:13:45.960Z"
                updated_at: "2023-12-24T18:13:49.280Z"
                status: PENDING
                message: Create a record in your DNS provider.
                dns_challenge:
                  name: _acme-challenge.example.com
                  type: CNAME
                  value: fpq2gpi42teg********.cm.yandexcloud.net.
              - domain: example.com
                type: DNS
                created_at: "2023-12-24T18:13:45.960Z"
                updated_at: "2023-12-24T18:13:49.280Z"
                status: PENDING
                message: Create a record in your DNS provider.
                dns_challenge:
                  name: _acme-challenge.example.com.
                  type: TXT
                  value: iiyJJJlsaFIqQ7DMUzira0OKU3iXuaqiN7U********
            ```

            For more information about the `yc certificate-manager certificate get` command, see the [CLI reference](../../cli/cli-ref/managed-services/certificate-manager/certificate/get.md).

            Save the values of the `value` fields from the `CNAME`-type sections under `challenges.dns_challenge` for both domain names. You will need them in the next step.

        1. Create CNAME [resource records](../../dns/concepts/resource-record.md) to pass the domain permission check:

            ```bash
            yc dns zone add-records \
              --name my-domain-zone \
              --record "_acme-challenge 600 CNAME <dns_challenge_value>" \
              --record "_acme-challenge.<subdomain> 600 CNAME <dns_challenge_value>"
            ```

            Where:
            * `<dns_challenge_value>`: Value saved in the previous step that is required to check permissions for the relevant domain using a CNAME record.
            * `<subdomain>`: Name that you gave to the CDN resource subdomain, e.g., `cdn`. In this case, the record will appear as follows: `_acme-challenge.cdn 600 CNAME fpq2gpi42teg********.cm.yandexcloud.net.`. 

            Result:

            ```text
            +--------+----------------------------------+-------+------------------------------------------+-----+
            | ACTION |              NAME                | TYPE  |                   DATA                   | TTL |
            +--------+----------------------------------+-------+------------------------------------------+-----+
            | +      | _acme-challenge.cdn.example.com. | CNAME | fpq2gpi42teg********.cm.yandexcloud.net. | 600 |
            | +      | _acme-challenge.example.com.     | CNAME | fpq2gpi42teg********.cm.yandexcloud.net. | 600 |
            +--------+----------------------------------+-------+------------------------------------------+-----+
            ```

            For more information about the `yc dns zone add-records` command, see the [CLI reference](../../cli/cli-ref/managed-services/dns/zone/add-records.md).

            The domain permission check may take from a few minutes to a few days. Wait until it is complete. As the result, the certificate will be issued and get the `Issued` status.

        1. Make sure that the certificate status has changed to `Issued`:

            ```bash
            yc certificate-manager certificate get \
              --name mymanagedcert
            ```

            Result:

            ```text
            id: fpqr2j0sdb1n********
            folder_id: b1gt6g8ht345********
            created_at: "2023-12-24T16:38:02.206Z"
            name: mymanagedcert
            type: MANAGED
            domains:
              - cdn.example.com
              - example.com
            status: ISSUED
            issuer: CN=R3,O=Let's Encrypt,C=US
            subject: CN=cdn.example.com
            serial: 4b7d7f0968097ae1a7707854a80********
            updated_at: "2023-12-24T16:46:03.578Z"
            issued_at: "2023-12-24T16:46:03.578Z"
            not_after: "2024-03-23T15:44:59Z"
            not_before: "2023-12-24T15:45:00Z"
            ```

    - API {#api}

      To get the information required to pass the permission check for a domain, use the [get](../../certificate-manager/api-ref/Certificate/get.md) REST API method for the [Certificate](../../certificate-manager/api-ref/Certificate/) resource or the [CertificateService/Get](../../certificate-manager/api-ref/grpc/certificate_service.md#Get) gRPC API call with the `view=FULL` flag.
      
      To create a CNAME resource record in a DNS zone, use the [updateRecordSets](../../dns/api-ref/DnsZone/updateRecordSets.md) REST API method for the [DnsZone](../../dns/api-ref/DnsZone/index.md) resource or the [DnsZoneService/UpdateRecordSets](../../dns/api-ref/grpc/dns_zone_service.md#UpdateRecordSets) gRPC API call.

    {% endlist %}

    {% note info %}

    {% include [checking-domain-rights-cname](../../_includes/certificate-manager/checking-domain-rights-cname.md) %}

    {% endnote %}


## Prepare a source bucket for the CDN resource {#setup-bucket-origin}.

1. Create an {{ objstorage-name }} bucket to be used as a [source](../../cdn/concepts/origins.md) for the CDN resource. The [bucket name](../../storage/concepts/bucket.md#naming) must be unique.

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
      1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}**.
      1. At the top right, click **{{ ui-key.yacloud.storage.buckets.button_create }}**.
      1. In the **{{ ui-key.yacloud.storage.bucket.settings.field_name }}** field, enter a name for the bucket, e.g., `cdn-source-bucket`.
      1. In the **{{ ui-key.yacloud.storage.bucket.settings.field_size-limit }}** field, specify `1 {{ ui-key.yacloud.common.units.label_gigabyte }}`.
      1. In the **{{ ui-key.yacloud.storage.bucket.settings.field_access-read }}** and **{{ ui-key.yacloud.storage.bucket.settings.field_access-list }}** fields, select `{{ ui-key.yacloud.storage.bucket.settings.access_value_public }}`.
      1. Click **{{ ui-key.yacloud.storage.buckets.create.button_create }}**.
      1. On the page with a list of buckets, select the bucket you created.
      1. In the left-hand menu, select ![website](../../_assets/console-icons/globe.svg) **{{ ui-key.yacloud.storage.bucket.switch_website }}**.
      1. Go to the **{{ ui-key.yacloud.storage.bucket.website.switch_hosting }}** section.
      1. In the **{{ ui-key.yacloud.storage.bucket.website.field_index }}** field, specify `index.html`.
      1. Click **{{ ui-key.yacloud.storage.bucket.website.button_save }}**.

    - {{ yandex-cloud }} CLI {#cli}

      1. Create a bucket:

          ```bash
          yc storage bucket create \
            --name <bucket_name> \
            --default-storage-class standard \
            --max-size 1073741824 \
            --public-read \
            --public-list
          ```

          Where `--name` is the bucket name, unique throughout {{ objstorage-name }}, e.g., `cdn-source-bucket`.

          Result:

          ```yaml
          name: cdn-source-bucket
          folder_id: b1gt6g8ht345********
          anonymous_access_flags:
            read: false
            list: false
          default_storage_class: STANDARD
          versioning: VERSIONING_DISABLED
          max_size: "1073741824"
          acl: {}
          created_at: "2023-12-22T18:11:23.028836Z"
          ```

          For more information about the `yc storage bucket create` command, see the [CLI reference](../../cli/cli-ref/managed-services/storage/bucket/create.md).

      1. Enable static website hosting in the bucket:

          ```bash
          yc storage bucket update \
            --name <bucket_name> \
            --website-settings '{"index": "index.html"}'
          ```

          Result:

          ```yaml
          name: cdn-source-bucket
          folder_id: b1gt6g8ht345********
          default_storage_class: STANDARD
          versioning: VERSIONING_DISABLED
          max_size: "1073741824"
          acl: {}
          created_at: "2023-12-23T09:56:58.249721Z"
          website_settings:
            redirect_all_requests: {}
          ```

          For more information about the `yc storage bucket update` command, see the [CLI reference](../../cli/cli-ref/managed-services/storage/bucket/update.md).

    - AWS CLI {#aws-cli}

      If you do not have the AWS CLI yet, [install and configure it](../../storage/tools/aws-cli.md).

      [Assign](../../iam/operations/sa/assign-role-for-sa.md) the `storage.editor` [role](../../storage/security/index.md#storage-editor) to the service account used by the AWS CLI.

      1. Create a bucket:

          ```bash
          aws s3api create-bucket \
            --endpoint-url https://{{ s3-storage-host }} \
            --bucket <bucket_name> \
            --acl public-read
          ```

          Where `--bucket` is the bucket name, unique throughout {{ objstorage-name }}, e.g., `cdn-source-bucket`.

          Result:

          ```json
          {
              "Location": "/cdn-source-bucket"
          }
          ```

      1. Enable static website hosting in the bucket:

          ```bash
          aws --endpoint-url https://storage.yandexcloud.net \
            s3 website "s3://<bucket_name>" \
            --index-document index.html
          ```

          Where `<file_name>`: Name of the previously created bucket e.g., `cdn-source-bucket`.

    - API {#api}

      To create a bucket, use the [create](../../storage/api-ref/Bucket/create.md) REST API method for the [Bucket](../../storage/api-ref/Bucket/index.md) resource, the [BucketService/Create](../../storage/api-ref/grpc/bucket_service.md#Create) gRPC API call, or the [create](../../storage/s3/api-ref/bucket/create.md) S3 API method.

      To enable static website hosting in the bucket, use the [update](../../storage/api-ref/Bucket/update.md) REST API method for the [Bucket](../../storage/api-ref/Bucket/index.md) resource, the [BucketService/Update](../../storage/api-ref/grpc/bucket_service.md#Update) gRPC API call, or the [upload](../../storage/s3/api-ref/hosting/upload.md) S3 API method.

    {% endlist %}

1. Upload the [prepared](#prepare-files) files to the bucket:

    {% list tabs group=instructions %}

    - Management console {#console}

      1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
      1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_storage }}**.
      1. Select the bucket you created ealier.
      1. In the top-right corner, click ![image](../../_assets/console-icons/arrow-up-from-line.svg) **{{ ui-key.yacloud.storage.bucket.button_upload }}** and select the files you created: `index.html` and `content.jpg`.
      1. In the window that opens, confirm the upload of the objects.

    - AWS CLI {#cli}

      1. Upload the `index.html` file to the bucket:

          ```bash
          aws --endpoint-url https://{{ s3-storage-host }} \
            s3 cp ./index.html s3://<bucket_name>/index.html
          ```

          Where `<file_name>`: Name of the previously created bucket e.g., `cdn-source-bucket`.

          Result:

          ```txt
          upload: ./index.html to s3://cdn-source-bucket/index.html
          ```

      1. Upload the `content.jpg` file to the bucket:

          ```bash
          aws --endpoint-url https://{{ s3-storage-host }} \
            s3 cp ./content.jpg s3://<bucket_name>/content.jpg
          ```

          Result:

          ```txt
          upload: ./content.jpg to s3://cdn-source-bucket/content.jpg
          ```

    - API {#api}

      To upload files to the bucket, use the [upload](../../storage/s3/api-ref/object/upload.md) S3 API method.

    {% endlist %}


## Create a CDN resource {#setup-cdn-resource}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.

  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_cdn }}**.

  1. {% include [activate-provider](../../_includes/cdn/activate-provider.md) %}

  1. Click **{{ ui-key.yacloud.cdn.button_resource-create }}**.

  1. Under **{{ ui-key.yacloud.cdn.label_section-content }}**, specify:

      * **{{ ui-key.yacloud.cdn.label_content-query-type }}**: `{{ ui-key.yacloud.cdn.value_query-type-one-origin }}`.
      * **{{ ui-key.yacloud.cdn.label_source-type }}**: `{{ ui-key.yacloud.cdn.value_source-type-url }}`.
      * In the **{{ ui-key.yacloud.cdn.field_origin }}** field, specify `<bucket_name>.{{ s3-web-host }}`, where `<bucket_name>` is the name of the previously created bucket used by the CDN resource as a source.

  1. Under **{{ ui-key.yacloud.cdn.label_section-domain }}**, in the **{{ ui-key.yacloud.cdn.label_personal-domain }}** field, specify the domain name you intend to assign to your CDN resource, e.g., `cdn.example.com`.

      At the bottom of **{{ ui-key.yacloud.cdn.label_section-domain }}**, the CNAME record value will appear. Copy this value as you will need it when creating a CNAME record for the CDN resource.

  1. Under **{{ ui-key.yacloud.cdn.label_section-additional }}**:

      1. In the **{{ ui-key.yacloud.cdn.label_protocol }}** field, select `{{ ui-key.yacloud.cdn.value_protocol-match }}`.
      1. In the **{{ ui-key.yacloud.cdn.label_redirect }}** field, select `{{ ui-key.yacloud.cdn.value_do-not-use }}`.
      1. In the **{{ ui-key.yacloud.cdn.label_certificate-type }}** field, select `{{ ui-key.yacloud.cdn.value_certificate-custom }}` and then select the previously created `mymanagedcert` certificate from the list that opens.
      1. In the **{{ ui-key.yacloud.cdn.label_host-header }}** field, select `{{ ui-key.yacloud.cdn.value_host-header-custom }}` and then, in the **{{ ui-key.yacloud.cdn.label_custom-host-header }}** field that appears, specify `<bucket_name>.{{ s3-web-host }}`, where `<bucket_name>` is the name of the previously created bucket used by the CDN resource as a source.
      1. Enable **{{ ui-key.yacloud.cdn.field_secure-key-enabled }}**.
      1. In the **{{ ui-key.yacloud.cdn.field_secure-key }}** that appears, specify a secret key, a string of 6 to 32 characters. It will be transmitted to the CDN resource configuration and used to generate and check signed links.
      1. In the **{{ ui-key.yacloud.cdn.field_secure-key-type }}** field, select `{{ ui-key.yacloud.cdn.value_secure-key-type-enable }}`.

  1. Click **{{ ui-key.yacloud.common.create }}**.

- {{ yandex-cloud }} CLI {#cli}

  1. If it is the first CDN resource you are creating, connect to the provider first:

      ```bash
      yc cdn provider activate \
        --type gcore
      ```

      For more information about the `yc cdn provider activate` command, see the [CLI reference](../../cli/cli-ref/managed-services/cdn/provider/activate.md).

  1. Create a resource:
  
      ```bash
      yc cdn resource create <subdomain_name> \
        --origin-custom-source <bucket_name>.{{ s3-web-host }} \
        --origin-protocol 'match' \
        --cert-manager-ssl-cert-id <certificate_ID> \
        --host-header <bucket_name>.{{ s3-web-host }} \
        --secure-key <secret_key> \
        --enable-ip-url-signing
      ```

      Where:
      * `<subdomain_name>`: Domain name for which the TLS certificate was created earlier and which will be used by the CDN resource, e.g., `cdn.example.com`.
      * `<bucket_name>`: Name of the previously created {{ objstorage-name }} bucket, e.g., `cdn-source-bucket`.
      * `--cert-manager-ssl-cert-id`: TLS certificate ID that was saved earlier when creating the certificate.
      * `--secure-key`: Secret key, a string of 6 to 32 characters. The secret key will be transmitted to the CDN resource configuration and used to generate and check signed links.

      Result:

      ```yaml
      id: bc8yqhobvxk6********
      folder_id: b1gt6g8ht345********
      cname: cdn.example.com
      created_at: "2024-06-22T19:59:05.430376Z"
      updated_at: "2024-06-22T19:59:05.430394Z"
      active: true
      options:
        edge_cache_settings:
          enabled: true
          default_value: "345600"
        query_params_options:
          ignore_query_string:
            enabled: true
            value: true
        host_options:
          host:
            enabled: true
            value: cdn-source-bucket.{{ s3-web-host }}
        stale:
          enabled: true
          value:
            - error
            - updating
        secure_key:
          enabled: true
          key: sdh********
          type: ENABLE_IP_SIGNING
      origin_group_id: "310699"
      origin_group_name: Origins for cdn.example.com (211929)
      origin_protocol: MATCH
      ssl_certificate:
        type: CM
        status: READY
        data:
          cm:
            id: fpqia0s2fc21********
      ```

      For more information about the `yc cdn resource create` command, see the [CLI reference](../../cli/cli-ref/managed-services/cdn/resource/create.md).

- API {#api}

  Use the [create](../../cdn/api-ref/Resource/create.md) REST API method for the [Resource](../../cdn/api-ref/Resource/index.md) resource or the [ResourceService/Create](../../cdn/api-ref/grpc/resource_service.md#Create) gRPC API call.

{% endlist %}

{% include [after-changes-tip](../../_includes/cdn/after-changes-tip.md) %}

The content on the new CDN resource will be accessible only via [signed links](../../cdn/concepts/secure-tokens.md#protected-link).

## Create a CNAME resource record for the CDN resource {#create-cdn-cname-record}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder to create your resources in.
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_dns }}**.
  1. Select the previously created DNS zone.
  1. Click **{{ ui-key.yacloud.dns.button_record-set-create }}**.
  1. Set the record parameters:

        * In the **{{ ui-key.yacloud.common.name }}** field, select `{{ ui-key.yacloud.dns.label_create-subdomain }}` and specify the name you gave to the CDN resource subdomain. For example, if the domain name of your CDN resource is `cdn.example.com`, put `cdn` only.
        * In the **{{ ui-key.yacloud.common.type }}** field, select `CNAME` as the [record type](../../dns/concepts/resource-record.md#rr-types).
        * In the **{{ ui-key.yacloud.dns.label_records }}** field, specify the CNAME record value you copied when creating the CDN resource.

  1. Click **{{ ui-key.yacloud.common.create }}**.

- {{ yandex-cloud }} CLI {#cli}

  1. Get the CNAME record value for the CDN resource:

      ```bash
      yc cdn resource get-provider-cname
      ```

      Result:

      ```yaml
      cname: cl-ms6*****90.edgecdn.ru
      folder_id: b1gt6g8ht345********
      ```

      For more information about the `yc cdn resource get-provider-cname` command, see the [CLI reference](../../cli/cli-ref/managed-services/cdn/resource/get-provider-cname.md).

      Save the obtained `cname` value: you will need it in the next step.

  1. Create a CNAME resource record in {{ dns-name }}:

      ```bash
      yc dns zone add-records \
        --name my-domain-zone \
        --record "<subdomain> 600 CNAME <cname_value>"
      ```

      Where:
      * `<subdomain>`: Subdomain created for the CDN resource. For example, for the `cdn.example.com` domain name, specify `cdn`.
      * `<cname_value>`: `cname` value for the CDN resource you obtained in the previous step.

      Result:

      ```text
      +--------+------------------+-------+--------------------------+-----+
      | ACTION |       NAME       | TYPE  |           DATA           | TTL |
      +--------+------------------+-------+--------------------------+-----+
      | +      | cdn.example.com. | CNAME | cl-ms6*****90.edgecdn.ru | 600 |
      +--------+------------------+-------+--------------------------+-----+
      ```

      For more information about the `yc dns zone add-records` command, see the [CLI reference](../../cli/cli-ref/managed-services/dns/zone/add-records.md).

- API {#api}

  To get the CNAME record value for a CDN resource, use the [getProviderCName](../../cdn/api-ref/Resource/getProviderCName.md) REST API method for the [Resource](../../cdn/api-ref/Resource/index.md) resource or the [ResourceService/GetProviderCName](../../cdn/api-ref/grpc/resource_service.md#GetProviderCName) gRPC API call.

  To create a CNAME resource record in a DNS zone, use the [updateRecordSets](../../dns/api-ref/DnsZone/updateRecordSets.md) REST API method for the [DnsZone](../../dns/api-ref/DnsZone/index.md) resource or the [DnsZoneService/UpdateRecordSets](../../dns/api-ref/grpc/dns_zone_service.md#UpdateRecordSets) gRPC API call.

{% endlist %}


## Publish the webiste on the web server {#publish-website}

Next, you will create and publish on your web server a simple website that will generate signed links to content hosted on the secure CDN resource. For data transfer security, you will also copy the previously created TLS certificate to the same web server and enable SSL encryption.


### Download the certificate from {{ certificate-manager-name }} {#export-certificate}

To use the TLS certificate created in {{ certificate-manager-name }} in your web server configuration, download the certificate chain and private key to the current directory:

{% list tabs group=instructions %}

- {{ yandex-cloud }} CLI {#cli}

  1. Learn the ID of the previously created TLS certificate:

      ```bash
      yc certificate-manager certificate list
      ```

      Result:

      ```text
      +----------------------+---------------+-----------------------------+---------------------+---------+--------+
      |          ID          |     NAME      |           DOMAINS           |      NOT AFTER      |  TYPE   | STATUS |
      +----------------------+---------------+-----------------------------+---------------------+---------+--------+
      | fpq90lobsh0l******** | mymanagedcert | cdn.example.com,example.com | 2024-03-22 16:42:53 | MANAGED | ISSUED |
      +----------------------+---------------+-----------------------------+---------------------+---------+--------+
      ```

      For more information about the `yc certificate-manager certificate list` command, see the [CLI reference](../../cli/cli-ref/managed-services/certificate-manager/certificate/list.md).

  1. Download the key and certificate by specifying the ID obtained in the previous step:

      ```bash
      yc certificate-manager certificate content \
        --id <certificate_ID> \
        --chain ./certificate_full_chain.pem \
        --key ./private_key.pem
      ```

      For more information about the `yc certificate-manager certificate content` command, see the [CLI reference](../../cli/cli-ref/managed-services/certificate-manager/certificate/content.md).

{% endlist %}


### Configure the web server {#setup-web-server}

1. Copy the obtained certificates and private key to the VM with the web server:

    ```bash
    scp ./certificate_full_chain.pem yc-user@<VM_IP_address>:certificate_full_chain.pem \
      && scp ./private_key.pem yc-user@<VM_IP_address>:private_key.pem
    ```

    Where `<VM_IP_address>` is the public IP address of the previously created VM with the web server.

    You can find the VM IP address in the **{{ ui-key.yacloud.compute.instance.overview.section_network }}** section on the VM page in the [management console]({{ link-console-main }}) or using this CLI command: `yc compute instance get mywebserver`.

    If this is your first time connecting to the VM, you will see an unknown host warning:

    ```text
    The authenticity of host '51.250.**.*** (51.250.**.***)' can't be established.
    ED25519 key fingerprint is SHA256:PpcKdcT09gjU045pkEIwIU8lAXXLpwJ6bKC********.
    This key is not known by any other names
    Are you sure you want to continue connecting (yes/no/[fingerprint])?
    ```

    Type `yes` in the terminal and press **Enter**.

1. [Connect](../../compute/operations/vm-connect/ssh.md) to the VM with the web server.

    ```bash
    ssh yc-user@<VM_IP_address>
    ```

1. Create a directory for the certificate and move the copied files there:

    ```bash
    sudo mkdir /etc/ssl-certificates
    sudo mv certificate_full_chain.pem /etc/ssl-certificates/
    sudo mv private_key.pem /etc/ssl-certificates/
    ```

1. Create a directory for your website files and grant the required permissions for it to the `www-data` user:

    ```bash
    sudo mkdir -p /var/www/<domain_name>/public_html
    sudo chown www-data:www-data /var/www/<domain_name>/public_html
    ```

    Where `<domain_name>` is the domain name of your website, e.g., `example.com`.

1. Configure a virtual host for your website:

    1. Create a virtual host configuration file:

        ```bash
        sudo nano /etc/apache2/sites-available/mywebsite.conf
        ```

    1. Add the following configuration into the file:

        ```text
        <VirtualHost *:443>
        ServerName <domain_name>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/<domain_name>/public_html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        SSLEngine on
        SSLCertificateFile /etc/ssl-certificates/certificate_full_chain.pem
        SSLCertificateChainFile /etc/ssl-certificates/certificate_full_chain.pem
        SSLCertificateKeyFile /etc/ssl-certificates/private_key.pem
        </VirtualHost>
        ```

        Where `<domain_name>` is the domain name of your website, e.g., `example.com`.

    1. Activate the virtual host you created:

        ```bash
        sudo a2ensite mywebsite
        ```

        Result:

        ```text
        Enabling site mywebsite.
        To activate the new configuration, you need to run:
          systemctl reload apache2
        ```

    1. Enable `ssl` for the web server:

        ```bash
        sudo a2enmod ssl
        ```

        Result:

        ```text
        Considering dependency setenvif for ssl:
        Module setenvif already enabled
        Considering dependency mime for ssl:
        Module mime already enabled
        Considering dependency socache_shmcb for ssl:
        Enabling module socache_shmcb.
        Enabling module ssl.
        See /usr/share/doc/apache2/README.Debian.gz on how to configure SSL and create self-signed certificates.
        To activate the new configuration, you need to run:
          systemctl restart apache2
        ```

    1. Restart the web server:

        ```bash
        sudo systemctl reload apache2
        ```


### Create a website {#create-website}

1. Create the home page file for the website:

    ```bash
    sudo nano /var/www/<domain_name>/public_html/index.php
    ```

    Where `<domain_name>` is the domain name of your website, e.g., `example.com`.

1. Add the following code into the `index.php` file you created:

    ```php
    <!DOCTYPE html>
    <html>
    <head>
      <title>Secure token generator website</title>
      <meta charset="utf-8" />
    </head>
    <body>

      <h2>Secure link generator</h2>
      <p>Below, a signed link to the secure CDN resource has been generated. The link is valid for five minutes. The referenced content is available only to the user the link was generated for by the website (verified by IP address).</p>
      <br>

      <?php

        $secret = '<secure_key>';
        $ip = trim(getUserIpAddr());
        $domain_name = '<cdn_domain_name>';
        $path = '<object_key>';
        $expires = time() + 300;
        $link = "$expires$path$ip $secret";
        $md5 = md5($link, true);
        $md5 = base64_encode($md5);
        $md5 = strtr($md5, '+/', '-_');
        $md5 = str_replace('=', '', $md5);
        $url = '<a href="https://'.$domain_name.$path.'?md5='.$md5.'&expires='.$expires.'" target="_blank">Signed link to file</a>';
    
        echo "<p>Your IP address: <b>".$ip."</b></p><p>If you are using a VPN, you link may not work. For the signed link generator to work properly, disable your VPN.</p>";
        echo "<br><br>";
        echo $url;
    
        function getUserIpAddr() {

            if (!empty($_SERVER['HTTP_CLIENT_IP'])) {
               $addr = $_SERVER['HTTP_CLIENT_IP'];
            } elseif (!empty($_SERVER['HTTP_X_FORWARDED_FOR'])) {
               $addr = $_SERVER['HTTP_X_FORWARDED_FOR'];
            } else {
               $addr = $_SERVER['REMOTE_ADDR'];
            }
            return $addr;
        }
      ?>

    </body>
    </html>
    ```

    Where:
    * `<secure_key>`: Secret key created when configuring the CDN resource.
    * `<cdn_domain_name>`: Domain name of the created CDN resource, e.g., `cdn.example.com`.
    * `<object_key>`: [Key of the object](../../storage/concepts/object.md#key) in the [source](../../cdn/concepts/origins.md) bucket, e.g., `/content.jpg`. The website generates a signed link to access this object via the CDN resource. 


## Test secure access to files {#check}

To test the generator of signed links to the secure CDN resource:
1. In your browser, go to the website you created, e.g., `https://example.com`.
1. Click the link that was generated.

    If everything works as it should, you will see the image hosted on the secure CDN resource.

    {% note info %}

    An active VPN may interfere with the signed link generator's operation. For the website to work correctly, disable your VPN.

    {% endnote %}

1. Open the generated link on another device that uses another IP address to access the internet, e.g., a smartphone.

    Access to content will be denied.

1. Try opening the link on the first device after the five-minute timeout expires.

    Access to content will be denied.

You have configured secure access to your content. 

When generating links, you can also specify a trusted IP address, e.g., an IP used for internet access in your corporate network. Thus you will restrict access to your content from outside your company’s network infrastructure.

## How to delete the resources you created {#clear-out}

To stop paying for the resources you created:
1. [Delete](../../cdn/operations/resources/delete-resource.md) the CDN resource, then [delete](../../cdn/operations/origin-groups/delete-group.md) the origin group.
1. [Delete](../../storage/operations/objects/delete.md) the objects you created in the bucket, then [delete](../../storage/operations/buckets/delete.md) the bucket itself.
1. [Delete](../../compute/operations/vm-control/vm-delete.md) the VM you created.
1. [Delete](../../dns/operations/zone-delete.md) the domain zone you created.
1. [Delete](../../certificate-manager/operations/managed/cert-delete.md) the TLS certificate you created.
1. [Delete](../../vpc/operations/security-group-delete.md) the security group you created, then [delete](../../vpc/operations/subnet-delete.md) the subnet you created, and finally, [delete](../../vpc/operations/network-delete.md) the network you created.