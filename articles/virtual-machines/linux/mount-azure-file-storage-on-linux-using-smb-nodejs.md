---
title: 通过 Azure CLI 1.0 使用 SMB 在 Linux VM 上装载 Azure 文件存储 | Microsoft 文档
description: 如何使用 SMB 在 Linux VM 上装载 Azure 文件存储
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 442c08a03ff3eb8e4c86f8190e16b74744aa9dd3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>通过 Azure CLI 1.0 使用 SMB 在 Linux VM 上装载 Azure 文件存储

本文介绍如何使用服务器消息块 (SMB) 协议将 Azure 文件存储装载在 Linux VM 上。 文件存储通过标准 SMB 协议在云中提供文件共享。 要求如下：

* 一个 [Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)
* [安全外壳 (SSH) 公钥和私钥文件](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>要使用的 CLI 版本
可以使用以下命令行界面 (CLI) 版本之一来完成任务：

- [Azure CLI 1.0](#quick-commands) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于资源管理部署模型的下一代 CLI


## <a name="quick-commands"></a>快速命令
若要快速完成任务，请按照本部分的步骤执行操作。 如下更多详细信息和上下文，请从[“详细演练”](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)部分开始。

### <a name="prerequisites"></a>先决条件
* 资源组
* Azure 虚拟网络
* 使用 SSH 入站类型的网络安全组
* 一个子网
* 一个 Azure 存储帐户
* Azure 存储帐户密钥
* 一个 Azure 文件存储共享
* 一个 Linux VM

将任何示例替换成自己的设置。

### <a name="create-a-directory-for-the-local-mount"></a>为本地装载创建目录

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>将文件存储 SMB 共享装载到装入点

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>在重新启动后保留装载
将以下行添加到 `/etc/fstab`：

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>详细演练

文件存储使用标准 SMB 协议在云中提供文件共享。 使用最新版本的文件存储，还可以从支持 SMB 3.0 的任何 OS 装载文件共享。 在 Linux 上使用 SMB 装载时，可轻松备份到 SLA 支持的可靠、永久的存档存储位置。

将文件从 VM 移至托管在文件存储上的 SMB 装载，可以很轻松地调试日志。 这是因为同一 SMB 共享可以通过本地方式装载到 Mac、Linux 或 Windows 工作站。 SMB 不会是实时流式处理 Linux 或应用程序日志的最佳解决方案，因为 SMB 协议并非为处理那样重的日志记录任务而构建。 专用统一的日志记录层工具（如 Fluentd）会是 SMB 之上的更好选择，可收集 Linux 和应用程序日志记录输出。

在此详细的演练中，我们创建所需的先决条件，即先创建文件存储共享，然后通过 SMB 将其装载到 Linux VM 上。

1. 使用以下代码创建 Azure 存储帐户：

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. 显示存储帐户密钥。

    创建存储帐户时，帐户密钥是成对创建的，这样是为了不中断任何服务就可轮换密钥。 轮换到密钥对中的第二个密钥后，将创建新的密钥对。 新的存储帐户密钥始终成对创建，可确保始终至少有一个未使用的存储密钥可以轮换到。 若要显示存储帐户密钥，请使用以下代码：

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. 创建文件存储共享。

    文件存储共享包含 SMB 共享。 配额始终以千兆字节 (GB) 表示。 若要创建文件存储共享，请使用以下代码：

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. 创建装载点目录。

    必须在 Linux 文件系统中创建将 SMB 共享装载到其中的本地目录。 写入到本地装载目录或从本地装载目录读取的任何内容都将转发到文件存储上托管的 SMB 共享。 若要创建该目录，请使用以下代码：

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. 使用以下代码装载 SMB 共享：

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. 重新启动后保留 SMB 装载。

    重新启动 Linux VM 后，已装载的 SMB 共享会在关闭过程中卸载。 若要在启动时重新装载 SMB 共享，必须向 Linux /etc/fstab 添加一行。 Linux 使用 fstab 文件列出在启动过程中需要装载的文件系统。 添加 SMB 共享可确保文件存储共享是 Linux VM 的永久装载文件系统。 使用 cloud-init 可以将文件存储 SMB 共享添加到新的 VM。

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>后续步骤

- [在创建期间使用 cloud-init 自定义 Linux VM](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [将磁盘添加到 Linux VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [使用 Azure CLI 加密 Linux VM 上的磁盘](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
