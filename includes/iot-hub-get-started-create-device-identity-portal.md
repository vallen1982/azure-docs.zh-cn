---
title: include 文件
description: include 文件
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>创建设备标识

本部分使用 [Azure 门户][lnk-azure-portal]在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]中的“标识注册表”部分。 使用门户中的“IoT 设备”面板为设备生成唯一设备 ID 和密钥，以用于在 IoT 中心中标识它本身。 设备 ID 区分大小写。

1. 请确保已登录到 [Azure 门户][lnk-azure-portal]。

1. 在跳转栏中单击“所有资源”，并查找 IoT 中心资源。

    ![导航到 IoT 中心][img-find-iothub]

1. 打开 IoT 中心资源后，单击“IoT 设备”工具，并单击顶部的“添加”。 提供新设备的名称（例如 **myDeviceId**），然后单击“保存”。

    ![在门户中创建设备标识][img-create-device]

   此操作会为 IoT 中心创建新设备标识。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 在“IoT 设备”的设备列表中，单击新创建的设备，记下“连接字符串---主密钥”。

    ![设备连接字符串][img-connection-string]

> [!NOTE]
> IoT 中心标识注册表只存储设备标识，以启用对 IoT 中心的安全访问。 它存储设备 ID 和密钥用作安全凭据，以及可用于禁用单个设备访问的启用/禁用标志。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

