---
title: Azure Active Directory B2C：Twitter 配置 | Microsoft Docs
description: 在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 Twitter 帐户的注册和登录功能。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/06/2017
ms.author: davidmu
ms.openlocfilehash: ee2d82f8c90b88a898428973a1febaa21034a14f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C：向用户提供使用 Twitter 帐户的注册和登录功能

## <a name="create-a-twitter-application"></a>创建 Twitter 应用程序
要将 Twitter 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Twitter 应用程序并向其提供合适的参数。 需要一个 Twitter 开发人员帐户，以便执行此操作。 如果没有账户，可在 [https://dev.twitter.com/](https://dev.twitter.com/) 处获取。

1. 访问 [Twitter 开发人员网站](https://dev.twitter.com/)并使用凭据登录。
2. 在“工具和支持”下单击“我的应用”，并单击“创建新应用”。 
3. 在表单中，提供**名称**、**说明**和**网站**的值。
4. 对于“回调 URL”，输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 确保将 **{tenant}** 替换为租户名称（例如 contosob2c.onmicrosoft.com）。
5. 选中框以同意**开发人员协议**，并单击“创建 Twitter 应用程序”。
6. 创建应用后，单击“密钥和访问令牌”。
7. 复制“使用者密钥”和“使用者密码”的值。 将 Twitter 配置为租户中的标识提供者时需要这两个值。

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>将 Twitter 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“Twitter”。
5. 单击“标识提供者类型”，选择“Twitter”，并单击“确定”。
6. 单击“设置此标识提供者”，针对“客户端 ID”，输入 Twitter **使用者密钥**，针对“客户端密码”输入 Twitter **使用者密码**。
7. 单击“确定”，并单击“创建”以保存 Twitter 配置。

## <a name="next-steps"></a>后续步骤

创建或编辑[内置策略](active-directory-b2c-reference-policies.md)并将 Twitter 添加为标识提供者。