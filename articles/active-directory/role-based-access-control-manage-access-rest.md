---
title: 使用 REST 进行基于角色的访问控制 - Azure AD | Microsoft 文档
description: 使用 REST API 管理基于角色的访问控制
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: rolyon
ms.openlocfilehash: ba25340e41cefe2b7847a39a6c9182cd0fc057d3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2018
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>使用 REST API 管理基于角色的访问控制
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

使用基于角色的访问控制 (RBAC) 时，可以通过分配特定范围的角色，为用户、组和服务主体定义访问权限。 本文介绍如何使用 REST API 管理访问权限。

## <a name="list-all-role-assignments"></a>列出所有角色分配
列出指定范围和子范围内的所有角色分配。

要列出角色分配，必须具有对 `Microsoft.Authorization/roleAssignments/read` 操作的范围内的访问权限。 所有内置角色均具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **GET** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

在 URI 中，进行以下替代来自定义请求：

1. 使用要列出角色分配的范围替换 *{scope}*。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用 2015-07-01 替换 *{api-version}*。
3. 使用筛选角色分配列表时要适用的条件替换 *{filter}*：

   * 只列出指定范围内的角色分配，而不包括子范围内的角色分配：`atScope()`    
   * 列出特定用户、组或应用程序的角色分配：`principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * 列出特定用户（包括继承自组的用户）的角色分配 | `assignedTo('{objectId of user}')`

### <a name="response"></a>响应
状态代码：200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>获取有关角色分配的信息
获取有关角色分配标识符指定的单个角色分配的信息。

要获取有关角色分配的信息，必须具有对 `Microsoft.Authorization/roleAssignments/read` 操作的访问权限。 所有内置角色均具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **GET** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

在 URI 中，进行以下替代来自定义请求：

1. 使用要列出角色分配的范围替换 *{scope}*。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用角色分配的 GUID 标识符替换 *{role-assignment-id}*。
3. 使用 2015-07-01 替换 *{api-version}*。

### <a name="response"></a>响应
状态代码：200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>创建角色分配
在指定范围内，为授予指定角色的指定主体创建角色分配。

要创建角色分配，必须具有对 `Microsoft.Authorization/roleAssignments/write` 操作的访问权限。 在内置角色中，只有“所有者”和“用户访问管理员”具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **PUT** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

在 URI 中，进行以下替代来自定义请求：

1. 使用要创建角色分配的范围替换 *{scope}*。 如果在父范围上创建角色分配，所有子范围将继承相同的角色分配。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1   
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用将成为新角色分配的 GUID 标识符的新 GUID 替换 *{role-assignment-id}*。
3. 使用 2015-07-01 替换 *{api-version}*。

对于请求正文，请提供以下格式的值：

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| 元素名称 | 必选 | Type | 说明 |
| --- | --- | --- | --- |
| roleDefinitionId |是 |String |角色的标识符。 标识符的格式为：`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |是 |String |角色将分配到的 Azure AD 主体（用户、组或服务主体）的 objectId。 |

### <a name="response"></a>响应
状态代码：201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>删除角色分配
删除指定范围的角色分配。

要删除角色分配，必须具有对 `Microsoft.Authorization/roleAssignments/delete` 操作的访问权限。 在内置角色中，只有“所有者”和“用户访问管理员”具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **DELETE** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

在 URI 中，进行以下替代来自定义请求：

1. 使用要创建角色分配的范围替换 *{scope}*。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用角色分配 ID GUID 替换 *{role-assignment-id}*。
3. 使用 2015-07-01 替换 *{api-version}*。

### <a name="response"></a>响应
状态代码：200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>列出所有角色
列出指定范围内可用于分配的所有角色。

要列出角色，必须在范围内具有对 `Microsoft.Authorization/roleDefinitions/read` 操作的访问权限。 所有内置角色均具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **GET** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

在 URI 中，进行以下替代来自定义请求：

1. 使用要列出角色的范围替换 *{scope}*。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用 2015-07-01 替换 *{api-version}*。
3. 使用要用于筛选角色列表的条件替换 *{filter}*：

   * 列出在指定范围及其任何子范围内可用于分配的角色：`atScopeAndBelow()`
   * 使用准确的显示名称搜索角色：`roleName%20eq%20'{role-display-name}'`。 使用角色的准确显示名称的 URL 编码形式。 例如，`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>响应
状态代码：200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>获取有关角色的信息
获取有关角色定义标识符指定的单个角色的信息。 若要获取有关使用显示名称的单个角色的信息，请参阅[列出所有角色](role-based-access-control-manage-access-rest.md#list-all-roles)。

要获取有关角色的信息，必须具有对 `Microsoft.Authorization/roleDefinitions/read` 操作的访问权限。 所有内置角色均具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **GET** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

在 URI 中，进行以下替代来自定义请求：

1. 使用要列出角色分配的范围替换 *{scope}*。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用角色定义的 GUID 标识符替换 *{role-definition-id}*。
3. 使用 2015-07-01 替换 *{api-version}*。

### <a name="response"></a>响应
状态代码：200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>创建自定义角色
创建自定义角色。

要创建自定义角色，必须在所有 `AssignableScopes` 内具有对 `Microsoft.Authorization/roleDefinitions/write` 操作的访问权限。 在内置角色中，只有“所有者”和“用户访问管理员”具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **PUT** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

在 URI 中，进行以下替代来自定义请求：

1. 使用自定义角色的第一个 *AssignableScope* 替换 *{scope}*。 下面的示例演示如何指定不同级别的范围。

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用将成为新自定义角色的 GUID 标识符的新 GUID 替换 *{role-definition-id}*。
3. 使用 2015-07-01 替换 *{api-version}*。

对于请求正文，请提供以下格式的值：

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 元素名称 | 必选 | Type | 说明 |
| --- | --- | --- | --- |
| 名称 |是 |String |自定义角色的 GUID 标识符。 |
| properties.roleName |是 |String |自定义角色的显示名称。 最大大小为 128 个字符。 |
| properties.description |否 |String |自定义角色的说明。 最大大小为 1024 个字符。 |
| properties.type |是 |String |设置为“CustomRole”。 |
| properties.permissions.actions |是 |String[] |操作字符串的数组，用于指定自定义角色授予访问权限的操作。 |
| properties.permissions.notActions |否 |String[] |操作字符串的数组，用于指定自定义角色不授予访问权限的操作。 |
| properties.assignableScopes |是 |String[] |范围的数组，可在这些范围中使用自定义角色。 |

### <a name="response"></a>响应
状态代码：201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>更新自定义角色
修改自定义角色。

要修改自定义角色，必须在所有 `AssignableScopes` 内具有对 `Microsoft.Authorization/roleDefinitions/write` 操作的访问权限。 在内置角色中，只有“所有者”和“用户访问管理员”具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **PUT** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

在 URI 中，进行以下替代来自定义请求：

1. 使用自定义角色的第一个 *AssignableScope* 替换 *{scope}*。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用自定义角色的 GUID 标识符替换 *{role-definition-id}*。
3. 使用 2015-07-01 替换 *{api-version}*。

对于请求正文，请提供以下格式的值：

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 元素名称 | 必选 | Type | 说明 |
| --- | --- | --- | --- |
| 名称 |是 |String |自定义角色的 GUID 标识符。 |
| properties.roleName |是 |String |更新的自定义角色的显示名称。 |
| properties.description |否 |String |更新的自定义角色的描述。 |
| properties.type |是 |String |设置为“CustomRole”。 |
| properties.permissions.actions |是 |String[] |操作字符串的数组，这些字符串指定更新的自定义角色授予访问权限的操作。 |
| properties.permissions.notActions |否 |String[] |操作字符串的数组，用于指定更新的自定义角色不授予访问权限的操作。 |
| properties.assignableScopes |是 |String[] |范围的数组，可在这些范围中使用更新的自定义角色。 |

### <a name="response"></a>响应
状态代码：201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>删除自定义角色
删除自定义角色。

要删除自定义角色，必须在所有 `AssignableScopes` 内具有对 `Microsoft.Authorization/roleDefinitions/delete` 操作的访问权限。 在内置角色中，只有“所有者”和“用户访问管理员”具有对此操作的访问权限。 有关角色分配和管理 Azure 资源的访问权限的详细信息，请参阅 [Azure 基于角色的访问控制](role-based-access-control-configure.md)。

### <a name="request"></a>请求
使用具有以下 URI 的 **DELETE** 方法：

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

在 URI 中，进行以下替代来自定义请求：

1. 使用要删除角色定义的范围替换 *{scope}*。 下面的示例演示如何指定不同级别的范围：

   * 资源：/subscriptions/{subscription-id}  
   * 资源组：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1  
   * 资源：/subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. 使用自定义角色的 GUID 角色定义 ID 替换 *{role-definition-id}*。
3. 使用 2015-07-01 替换 *{api-version}*。

### <a name="response"></a>响应
状态代码：200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
