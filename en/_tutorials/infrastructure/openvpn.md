---
title: "OpenVPN. Creating a VPN connection"
keywords:
  - openvpn
  - openvpn setup
  - open vpn
  - vpn server setup
  - vpn connection
  - vpn connection
---

# Connecting to a cloud network using OpenVPN

With TCP or UDP port tunnels and asymmetric encryption, you can create virtual networks. For example, you can use VPN to do the following:

* Connect geographically remote networks.
* Connect freelancers to the office network.
* Set up an encrypted connection over an open Wi-Fi network.

[OpenVPN Access Server](/marketplace/products/yc/openvpn-access-server) is compatible with the [open-source version](https://github.com/OpenVPN) of OpenVPN and built on its basis. It provides clients for Windows, Mac, Android, and iOS and is used to manage connections using a web interface.

An example of auto-connect and login-and-password configurations is shown below. To create a virtual network:

1. [Prepare your cloud](#before-you-begin).
1. [Create subnets and a test VM](#create-environment).
1. [Start the VPN server](#create-vpn-server).
1. [Configure network traffic permissions](#network-settings).
1. [Get the administrator password](#get-admin-password).
1. [Activate license](#get-license).
1. [Create an OpenVPN user](#configure-openvpn).
1. [Connect to the VPN](#test-vpn).

If you no longer need the VPN server, [delete the VM](#clear-out).

## Prepare your cloud {#before-you-begin}

{% include [before-you-begin](../_tutorials_includes/before-you-begin.md) %}


### Required paid resources {#paid-resources}

The cost of infrastructure support for OpenVPN includes:

* Fee for the disks and continuously running VMs (see [{{ compute-full-name }} pricing](../../compute/pricing.md)).
* Fee for using a dynamic or static external IP address (see [{{ vpc-full-name }} pricing](../../vpc/pricing.md)).
* Fee for the OpenVPN Access Server license (when using more than two connections).


## Create subnets and a test VM {#create-environment}

To connect cloud resources to the internet, make sure you have [networks](../../vpc/operations/network-create.md) and [subnets](../../vpc/operations/subnet-create.md).

Create a test [VM](../../compute/operations/vm-create/create-linux-vm.md) without a public IP address and connect it to the subnet.

## Start the VPN server {#create-vpn-server}

Create a VM to be the gateway for VPN connections:

{% list tabs group=instructions %}

- Management console {#console}

  1. On the folder page in the [management console]({{ link-console-main }}), click **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}** in the top-right corner and select **{{ ui-key.yacloud.iam.folder.dashboard.value_compute }}**.
  1. Enter `vpn-server` as your VM name and add a description.
  1. Select the [availability zone](../../overview/concepts/geo-scope.md) where the test VM is already located.
  1. Under **{{ ui-key.yacloud.compute.instances.create.section_image }}**, go to the **{{ ui-key.yacloud.compute.instances.create.image_value_marketplace }}** tab and select the [OpenVPN Access Server](/marketplace/products/yc/openvpn-access-server) image.
  1. Under **{{ ui-key.yacloud.compute.instances.create.section_storages }}**, enter `20 {{ ui-key.yacloud.common.units.label_gigabyte }}` as your disk size.
  1. Under **{{ ui-key.yacloud.compute.instances.create.section_platform }}**, navigate to the **{{ ui-key.yacloud.component.compute.resources.label_tab-custom }}** tab and specify:

      * **{{ ui-key.yacloud.component.compute.resources.field_platform }}**: `Intel Ice Lake`
      * **{{ ui-key.yacloud.component.compute.resources.field_cores }}**: `2`
      * **{{ ui-key.yacloud.component.compute.resources.field_memory }}**: `2 {{ ui-key.yacloud.common.units.label_gigabyte }}`

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_network }}**:

      * Select the required network and subnet and assign a public IP address to the VM either by selecting it from the list or automatically.

          Either use static public IP addresses [from the list](../../vpc/operations/get-static-ip) or [convert](../../vpc/operations/set-static-ip) the VM IP address to static. Dynamic IP addresses may change after the VM reboots and the connections will no longer work.

      * If a list of **{{ ui-key.yacloud.component.compute.network-select.field_security-groups }}** is available, select a [security group](../../vpc/concepts/security-groups.md). If you leave this field empty, the [default security group](../../vpc/concepts/security-groups.md#default-security-group) will be assigned.

  1. Under **{{ ui-key.yacloud.compute.instances.create.section_access }}**, specify the data for access to the VM:

      * In the **{{ ui-key.yacloud.compute.instances.create.field_user }}** field, enter the SSH username, e.g., `yc-user`.
      * In the **{{ ui-key.yacloud.compute.instances.create.field_key }}** field, paste the contents of the public key file.

          You will need to create a key pair for the SSH connection yourself; see [{#T}](../../compute/operations/vm-connect/ssh.md#creating-ssh-keys) for details.

  1. Click **{{ ui-key.yacloud.compute.instances.create.button_create }}**.
  1. A window will open informing you of the pricing type, which is BYOL (Bring Your Own License). Click **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

## Configure network traffic permissions {#network-settings}

{% include [openvpn-network-settings](../_tutorials_includes/openvpn-network-settings.md) %}

## Get the administrator password {#get-admin-password}

{% include [openvpn-get-admin-password](../_tutorials_includes/openvpn-get-admin-password.md) %}

## Activate license {#get-license}

{% include [openvpn-activate-license](../_tutorials_includes/openvpn-activate-license.md) %}

## Create an OpenVPN user {#configure-openvpn}

{% include [openvpn-create-user](../_tutorials_includes/openvpn-create-user.md) %}

## Connect to the VPN {#test-vpn}

In the admin panel, you can download [OpenVPN Connect](https://openvpn.net/vpn-client/) for Windows, Linux, MacOS, Android, iOS. You can also use [OpenSource clients](https://openvpn.net/source-code/) for connection.
 
To make sure the connection is established and working properly, connect to the VPN and run the `ping` command for the internal address of the test VM:

{% list tabs group=operating_system %}

- Linux {#linux}

   1. Install `openvpn` using the package manager:

      ```bash
      sudo apt update && sudo apt install openvpn
      ```

   1. Allow automatic connection for the `test-user` user:

      * Log in to the admin panel at `https://<VM_public_IP_address>/admin/`.
      * Open the **User management** → **User permissions** tab.
      * Enable the **Allow Auto-login** option in the user line.

   1. Configure routing:

      * Log in to the admin panel at `https://<VM_public_IP_address>/admin/`.
      * Open the **Configuration** → **VPN Settings** tab.
      * Under **Routing**, disable the **Should client Internet traffic be routed through the VPN?** option.

   1. Download a configuration profile:

      * In your browser, open the user panel at `https://<VM_public_IP_address>/`.
      * Sign in using the `test-user` username and password.
      * In the **Available Connection Profiles** section, click **Yourself (autologin profile)** and download the `profile-1.ovpn` file.
      * You can also download a configuration file in the admin panel at `https://<<VM_public_IP_address>/admin/`.

   1. Upload the configuration file to a Linux machine:

      ```bash
      scp profile-1.ovpn user@<IP address>:~
      ```

   1. Move the configuration file to the `/etc/openvpn` folder:

      ```bash
      sudo mv /home/user/profile-1.ovpn /etc/openvpn
      ```

   1. Change the file extension from `ovpn` to `conf`:

      ```bash
      sudo mv /etc/openvpn/profile-1.ovpn /etc/openvpn/profile-1.conf
      ```

   1. Close access to the file:
 
      ```bash
      sudo chown root:root /etc/openvpn/profile-1.conf
      sudo chmod 600 /etc/openvpn/profile-1.conf
      ```

   1. The VPN connection will turn on automatically after restarting. To start the connection manually, run the command:

      ```bash
      sudo openvpn --config /etc/openvpn/profile-1.conf
      ```

      Result:
   
      ```text
      2022-04-05 15:35:49 DEPRECATED OPTION: --cipher set to 'AES-256-CBC' but missing in --data-ciphers (AES-256-GCM:AES-128-GCM). Future OpenVPN version will ignore --cipher for cipher negotiations. Add 'AES-256-CBC' to --data-ciphers or change --cipher 'AES-256-CBC' to --data-ciphers-fallback 'AES-256-CBC' to silence this warning.
      2022-04-05 15:35:49 OpenVPN 2.5.1 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on May 14 2021
      2022-04-05 15:35:49 library versions: OpenSSL 1.1.1k  25 Mar 2021, LZO 2.10
      2022-04-05 15:35:49 Outgoing Control Channel Encryption: Cipher 'AES-256-CTR' initialized with 256 bit key
      2022-04-05 15:35:49 Outgoing Control Channel Encryption: Using 256 bit message hash 'SHA256' for HMAC authentication
      2022-04-05 15:35:49 Incoming Control Channel Encryption: Cipher 'AES-256-CTR' initialized with 256 bit key
      2022-04-05 15:35:49 Incoming Control Channel Encryption: Using 256 bit message hash 'SHA256' for HMAC authentication
      2022-04-05 15:35:49 TCP/UDP: Preserving recently used remote address: [AF_INET]51.250.25.105:443
      2022-04-05 15:35:49 Socket Buffers: R=[131072->131072] S=[16384->16384]
      2022-04-05 15:35:49 Attempting to establish TCP connection with [AF_INET]51.250.25.105:443 [nonblock]
      ...
      ...
      2022-04-05 15:35:54 Initialization Sequence Completed
      ```

   1. Test the network using the `ping` command:

      ```bash
      sudo ping <test_VM_internal_IP_address>
      ```

      If the command is executed, the VM can be accessed via OpenVPN.

   1. To terminate a manually established connection, press **Ctrl** + **C**.

- Windows {#windows}

   1. Download the installation distribution:

      * In your browser, open the user panel at `https://<VM_public_IP_address>/`.
      * Sign in using the `test-user` username and password.
      * Download OpenVPN Connect version 2 or 3 by clicking the Windows icon.

   1. Install and run OpenVPN Connect.

   1. A VPN connection will turn on automatically if auto-login is enabled in the user profile.

   1. You can import a new configuration profile into the application. To do this, specify the `https://<VM_public_IP_address>/` URL or select a profile file.

   1. Open the terminal and run this command: `ping <internal_IP_address_of_test_VM>`. If the command is running, the VM can be accessed via VPN.

- macOS {#macos}

   1. Download the installation distribution:

      * In your browser, open the user panel at `https://<VM_public_IP_address>/`.
      * Sign in using the `test-user` username and password.
      * Download OpenVPN Connect version 2 or 3 by clicking the Apple icon.

   1. Install and run OpenVPN Connect.

   1. A VPN connection will turn on automatically if auto-login is enabled in the user profile.

   1. You can import a new configuration profile into the application. To do this, specify the `https://<<VM_public_IP_address>/` URL or select a profile file.

   1. Open the terminal and run this command: `ping <internal_IP_address_of_test_VM>`. If the command is running, the VM can be accessed via VPN.

{% endlist %}

## How to delete the resources you created {#clear-out}

Delete the resources you no longer need to avoid paying for them:

* [Delete](../../compute/operations/vm-control/vm-delete.md) the VM called `vpn-server` and test VMs.
* If you reserved a public static IP address, [delete it](../../vpc/operations/address-delete.md).

#### See also {#see-also}

* [OpenVPN Project Wiki](https://community.openvpn.net/openvpn/wiki)
* [{#T}](../../certificate-manager/operations/managed/cert-get-content.md)
* [Connecting to Access Server](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-linux/#openvpn-open-source-openvpn-cli-program)


