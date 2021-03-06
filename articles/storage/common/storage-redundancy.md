---
title: Azure 存储中的数据复制 | Microsoft Docs
description: 复制 Microsoft Azure 存储帐户中的数据以实现持久性和高可用性。 复制选项包括本地冗余存储 (LRS)、区域冗余存储 (ZRS)、异地冗余存储 (GRS) 和读取访问异地冗余存储 (RA-GRS)。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: bdb9bfaa85f526af0c5e42294a75664fa7137849
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="azure-storage-replication"></a>Azure 存储复制

始终复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 Azure 存储复制功能可以复制数据，以防范各种计划内和计划外的事件，例如暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心甚至跨区域复制数据。

即使面临故障时，复制也可确保存储帐户满足[存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/)的要求。 请参阅 SLA，了解有关 Azure 存储确保持续性和可用性的信息。

## <a name="choosing-a-replication-option"></a>选择复制选项

创建存储帐户时，可以选择以下复制选项之一：

* [本地冗余存储 (LRS)](storage-redundancy-lrs.md)
* [区域冗余存储空间 (ZRS)](storage-redundancy-zrs.md)
* [异地冗余存储 (GRS)](storage-redundancy-grs.md)
* [读取访问异地冗余存储 (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

下表简要概述了每种复制策略为给定类型的事件（或具有类似影响性的事件）提供的持久性和可用性范围。

| 方案 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 数据中心内的节点不可用 |是 |是 |是 |是
| 整个数据中心（区域性或非区域性）不可用 |否 |是 |是 |是 |
| 区域范围的服务中断 |否 |否 |是 |是 |
| 整个区域不可用时对数据进行读取访问（远程异地复制区域中） |否 |否 |否 |是 |
| 旨在给定年份为对象提供 ___ 的持续性 |至少为 99.999999999% (11 9's)|至少为 99.9999999999% (12 9's)|至少为 99.99999999999999% (16 9's)|至少为 99.99999999999999% (16 9's)|
| 提供 ___ 种存储帐户类型 |GPv1、GPv2、Blob |GPv2 |GPv1、GPv2、Blob |GPv1、GPv2、Blob

有关不同冗余选项的定价信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

> [!NOTE]
> 高级存储仅支持本地冗余存储 (LRS)。 有关高级存储的信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../../virtual-machines/windows/premium-storage.md)。

## <a name="changing-replication-strategy"></a>更改复制策略
我们允许使用 [Azure 门户](https://portal.azure.com/)、[Azure Powershell](storage-powershell-guide-full.md)、[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) 或众多的 [Azure 客户端库](https://docs.microsoft.com/en-us/azure/index?view=azure-dotnet#pivot=sdkstools)之一来更改存储帐户的复制策略。 更改存储帐户的复制类型不会导致停机。

   > [!NOTE]
   > 目前，无法使用门户或 API 将帐户转换为 ZRS。 但是，我们已计划在推出 ZRS 正式版后，支持从 LRS、GRS 和 RA-GRS 迁移到 ZRS。 有关详细信息，请参阅[区域冗余存储 (ZRS)](storage-redundancy-zrs.md)。
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>更改帐户的复制策略是否产生任何费用？
这取决于转换路径。 从最便宜到最昂贵的冗余产品/服务依次为 LRS、ZRS、GRS 和 RA-GRS。 例如，从 LRS 转移到其他任何存储会产生额外的费用，因为这是转移到更高级的冗余级别。 转移到 GRS 或 RA-GRS 会产生出口带宽费用，因为（主要区域中的）数据将复制到远程次要区域。 这是在初始设置期间收取的一次性费用。 复制数据后，无需进一步支付转换费用。 只有在复制任何新数据，或者复制现有数据的更新时才要付费。 有关带宽费用的详细信息，请参阅 [Azure 存储定价页面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

如果从 GRS 更改为 LRS，则不会产生额外的费用，但从次要位置复制的数据将被删除。

## <a name="see-also"></a>另请参阅

- [本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](storage-redundancy-lrs.md)
- [区域冗余存储 (ZRS)：具有高可用性的 Azure 存储应用程序](storage-redundancy-zrs.md)
- [异地冗余存储 (GRS)：Azure 存储的跨区域复制](storage-redundancy-grs.md)
- [Azure 存储可伸缩性和性能目标](storage-scalability-targets.md)
- [使用 RA-GRS 存储设计高度可用的应用程序](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundancy options and read access geo redundant storage ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)（Microsoft Azure 存储冗余选项和读取访问异地冗余存储）
- [SOSP Paper - Azure Storage: A highly available cloud storage service with strong consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)（SOSP 论文 - Azure 存储：具有高度一致性的高可用性云存储服务）
