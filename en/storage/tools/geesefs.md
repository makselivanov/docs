# GeeseFS

[GeeseFS](https://github.com/yandex-cloud/geesefs) is a utility for mounting {{ objstorage-name }} buckets using [FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) on Linux, MacOS, and Windows. It was developed by the {{ yandex-cloud }} team as a fork of [goofys](goofys.md). It is a high-performance, POSIX-ish file system.

## Functionality {#features}

### Performance {#performance}

Compared to goofys and [s3fs](s3fs.md), GeeseFS handles large amounts of small files (up to 1 MB) much faster and achieves similar or higher performance with large files. For more information about benchmark tests, see the [GeeseFS repository on GitHub](https://github.com/yandex-cloud/geesefs/tree/master/bench).

To make GeeseFS run faster, it implements:

* Parallel readahead.
* Heuristic readahead for random access: if the size of multiple blocks requested in a row is below the threshold, GeeseFS downloads smaller blocks from storage for upcoming requests.
* Parallel [multipart uploads](../concepts/multipart.md) of objects to storage.
* Optimized object updates: the client and repository only exchange modified object parts.
* Background uploads of small object trees and [directories](../concepts/object.md#folder): when a directory is requested, GeeseFS downloads the whole tree per request to storage.
* Asynchronous object write, rename, and delete.
* Disk cache for reads and writes.

### POSIX compatibility {#posix-compatibility}

In addition to the basic functions of the POSIX standards (`open`, `read`, `write`, `close`, and so on), GeeseFS supports the following features:

* Read-after-write consistency.
* Partial writes (please note that partial writes in buckets with [versioning](../concepts/versioning.md) may result in intermediate object versions).
* `fsync`: Synchronization of the contents of an object or directory between the VM memory and storage.
* `truncate`: Changing object size at will.
* Soft links (symlinks).
* `xattr`: Extended file attributes.
* Directory renames.
* `readdir`: Reads of directory metadata.

### Partial updating and appending of objects data {#patch}

GeeseFS supports [partial updating and appending of objects data](../concepts/object-patch.md) to {{ objstorage-name }} buckets.

To enable partial object updates, use the `--enable-patch` option.

To learn more, see the GeeseFS repository on GitHub:
* [Partial object updates](https://github.com/yandex-cloud/geesefs#partial-object-updates-patch): Description of partial updating and appending of objects data.
* [Concurrent Updates](https://github.com/yandex-cloud/geesefs#concurrent-patch): Description of how an object can be partially updated by multiple concurrent requests.

### Limitations {#restrictions}

GeeseFS does not support the following:

* Working with file and directory access permissions, including the `chmod` and `chown` commands. 

  When mounting the file system, you can specify:

  * Access permissions to all files or directories in the `--file-mode` and `--dir-mode` option values, respectively.
  * ID of the owner of all files and directories in the `--uid` option value.
  * ID of the group all files and directories belong to in the `--gid` option value.

  For example:

  ```bash
  geesefs \
    --file-mode=0666 \
    --dir-mode=0777 \
    --uid=1000 \
    <bucket_name> <mount_point>
  ```

* Hard links.
* File locking.
* Correct time of the last access to the file (`atime`) and the last change of the file's attributes (`ctime`). Both fields always contain the time of the file's last modification: same as in the `mtime` field.
* Creating files larger than 1 TB.

## Getting started {#before-you-begin}

{% include [aws-tools-prepare](../../_includes/aws-tools/aws-tools-prepare.md) %}

{% include [access-bucket-sa](../../_includes/storage/access-bucket-sa.md) %}

## Installation {#installation}

{% list tabs group=operating_system %}

- Linux {#linux}

  1. Install the utilities required by FUSE, e.g.:

     * Debian, Ubuntu:

       ```bash
       sudo apt-get install fuse
       ```

     * CentOS:

       ```bash
       sudo yum install fuse
       ```

  1. Download and install GeeseFS:

     ```bash
     wget https://github.com/yandex-cloud/geesefs/releases/latest/download/geesefs-linux-amd64
     chmod a+x geesefs-linux-amd64
     sudo cp geesefs-linux-amd64 /usr/bin/geesefs
     ```

- macOS {#macos}

  1. Install the [macFUSE](https://osxfuse.github.io/) package. For more information, see the [installation guide](https://github.com/osxfuse/osxfuse/wiki/FAQ#2-installuninstall-questions) in the macFUSE repository on GitHub.
  1. Download and install GeeseFS:

     ```bash
     platform='arm64'
     if [[ $(uname -m) == 'x86_64' ]]; then platform='amd64'; fi
     wget https://github.com/yandex-cloud/geesefs/releases/latest/download/geesefs-mac-$platform
     chmod a+x geesefs-mac-$platform
     sudo cp geesefs-mac-$platform /usr/bin/geesefs
     ```

- Windows {#windows}

  1. [Download](https://winfsp.dev/rel/) and install WinFSP.
  1. [Download](https://github.com/yandex-cloud/geesefs/releases/latest/download/geesefs-win-x64.exe) the `geesefs-win-x64.exe` file.
  1. Rename `geesefs-win-x64.exe` to `geesefs.exe` for convenience.

{% endlist %}

You can also build GeeseFS yourself using its source code. For more information, see the [guide](https://github.com/yandex-cloud/geesefs#installation) in the GeeseFS repository on GitHub.

## Authentication {#authentication}


GeeseFS uses a [static access key](../../iam/concepts/authorization/access-key.md) for {{ objstorage-name }}. You can set it using one of the following methods:

{% list tabs group=operating_system %}

- Linux/macOS {#linux-macos}

  * Using the `credentials` [file](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html), which you need to put into the `~/.aws/` folder:

    ```text
    [default]
      aws_access_key_id=<key_ID>
      aws_secret_access_key=<secret_key>
    ```

    If the key file is located elsewhere, specify its path in the `--shared-config` parameter when mounting the bucket:

    ```bash
    geesefs \
      --shared-config <path_to_key_file> \
      <bucket_name> <mount_point>
    ```

  * Using environment variables:

    ```bash
    export AWS_ACCESS_KEY_ID=<key_ID>
    export AWS_SECRET_ACCESS_KEY=<secret_key>
    ```

  {% note info %}

  You can run the `geesefs` command with superuser privileges (`sudo`). In which case make sure to send information about the key either in the `--shared-config` parameter or using environment variables.

  {% endnote %}

- Windows {#windows}

  * Using the `credentials` [file](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html), which you need to put into: `users/<current_user>/.aws/`:

    ```text
    [default]
      aws_access_key_id=<key_ID>
      aws_secret_access_key=<secret_key>
    ```

    If the key file is located elsewhere, specify its path in the `--shared-config` parameter when mounting the bucket:

    ```cmd
    geesefs <bucket_name> <mount_point> ^
      --shared-config <path_to_key_file>
    ```

  * Using environment variables:

    ```cmd
    set AWS_ACCESS_KEY_ID=<key_ID>
    set AWS_SECRET_ACCESS_KEY=<secret_key>
    ```

{% endlist %}

When using GeeseFS on a {{ compute-name }} VM that has a[linked service account](../../compute/operations/vm-connect/auth-inside-vm.md#link-sa-with-instance), you can enable simplified authentication that does not require a static access key. To do this, use the `--iam` option when mounting the bucket.

## Mounting a bucket {#bucket-mounting}

Select the folder or disk where you want to mount the bucket. Make sure you have sufficient rights to perform this operation.

When mounting a bucket, you can also configure GeeseFS settings related to system [performance](#performance) and object access rights. To view the list of options and their descriptions, run `geesefs --help`.

* For one-time bucket mounting, run the following command:

    ```bash
    geesefs <bucket_name> <mount_point>
    ```

* To automatically mount a bucket at system startup:

  {% list tabs group=operating_system %}

  - Linux/macOS {#linux-macos}

    1. Add the following line to the `/etc/fuse.conf` file:

        ```text
        user_allow_other
        ```

    1. Add the following line to the `/etc/fstab` file:

        ```text
        <bucket_name>    <mount_point>    fuse.geesefs    _netdev,allow_other,--file-mode=0666,--dir-mode=0777    0   0
        ```

    {% note info %}

    To ensure that the bucket is mounted correctly, provide the full absolute path to the mount point without `~`, e.g., `/home/user/mountpoint`.

    {% endnote %}

  - Windows {#windows}

    Create a Windows service that will automatically run at system startup:

    1. Run `CMD` as an administrator.
    1. Run this command:

        ```cmd
        sc create <service_name> ^
          binPath="<command_for_mounting>" ^
          DisplayName= "<service_name>" ^
          type=own ^
          start=auto
        ```

        Where `binPath` is the path to the `geesefs.exe` file with the required mounting parameters. For example: `C:\geesefs\geesefs.exe <bucket_name> <mount_point>`.

        Result:

        ```text
        [SC] CreateService: Success
        ```

    1. Click **Start** and start typing `Services` in the Windows search bar. Run the **Services** application as an administrator.
    1. In the window that opens, find the service you created earlier, right-click it, and select **Properties**.
    1. On the **Log on** tab, select **This account** and specify your Windows account name and password.

        If necessary, click **Browse** → **Advanced** → **Search** to find the user you need on the computer.

    1. Click **OK**.

    To delete the created service, open `CMD` as an administrator and run the following command:

    ```cmd
    sc delete <service_name>
    ```

    Result:

    ```text
    [SC] DeleteService: Success
    ```

  {% endlist %}
