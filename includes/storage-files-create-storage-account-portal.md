---
title: storage-files-create-storage-account-portal
description: 如何为 Azure 文件创建存储帐户
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
存储帐户是一个存储共享池，在其中可以部署 Azure 文件共享或其他存储资源，例如 Blob 或队列。 一个存储帐户可以包含无限数量的共享，一个共享可以存储无限数量的文件，直到达到存储帐户的容量限制为止。

创建存储帐户：

1. 在左侧菜单中，单击 **+** 创建资源。
2. 在搜索框中键入“存储帐户”(1)，选择“存储帐户 - Blob、文件、表、队列”(2)，然后单击“创建”。
    ![资源搜索对话框中显示的存储帐户条目的屏幕截图](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. 在“名称”中，键入 *mystorageacct* 并后接一些随机数字，直到出现绿色复选标记，表示该名称是唯一的。 存储帐户名称必须全部为小写，且全局唯一。 记下存储帐户名称，因为稍后要用到。 
4. 在“部署模型”中，保留默认值“资源管理器”。 若要详细了解 Azure 资源管理器部署与经典部署之间的差别，请参阅[了解部署模型和资源状态](../articles/azure-resource-manager/resource-manager-deployment-model.md)。
5. 在“帐户类型”中，选择“StorageV2”。 若要详细了解不同类型的存储帐户，请参阅[了解 Azure 存储帐户](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
6. 在“性能”，保留默认值“标准存储”。 Azure 文件目前仅支持标准存储；即使选择高级存储，文件共享也会存储在标准存储中。
7. 在“复制”中，选择“本地冗余存储(LRS)”。 
8. 在“需要安全传输”中，我们建议始终选择“已启用”。 若要详细了解此选项，请参阅[了解传输中加密](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
9. 在“订阅”中，选择要在其中创建存储帐户的订阅。 如果只有一个订阅，则默认值应是该订阅。
10. 在“资源组”中，选择“新建”并键入 *myResourceGroup* 作为名称。
11. 在“位置”中，选择“美国东部”。
12. 在“虚拟网络”中，保留默认选项“已禁用”。 
13. 选择“固定到仪表板”，以便轻松找到该存储帐户。
14. 完成后，单击“创建”开始部署。