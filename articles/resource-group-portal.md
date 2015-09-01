<properties
	pageTitle="使用 Azure 门户管理 Azure 资源"
	description="将多个资源分组为一个逻辑组，该逻辑组将成为其包含的资源的生命周期边界。"
	services="azure-portal"
	documentationCenter=""
	authors=""
	writer="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-portal"
	ms.date="04/24/2015"
	wacn.date=""/>


# 使用 Azure 门户管理 Azure 资源

## 介绍

过去，在 Microsoft Azure 中管理资源（用户托管的实体，例如数据库服务器、数据库或网站）时，每次只能对一个资源执行操作。如果某个复杂应用程序由多个资源构成，对此应用程序的管理就会变得很复杂。在 Azure 门户中，你可以创建资源组，以统一管理应用程序中的所有资源。资源组是 Azure 中的新概念，可充当该组中每个资源的生命周期界限。

资源组可让你统一管理应用程序中的所有资源。资源组由新的管理功能 Azure 资源管理器启用。使用 Azure 资源管理器可将多个资源划分成一个逻辑组，充当组中每个资源的生命周期界限。通常，一个组将包含与特定应用程序相关的资源。例如，组可以包含用于托管公共网站的网站资源、用于存储站点所用关系数据的 SQL Database，以及用于存储非关系资产的存储帐户。

下面简要概述了如何在 Azure 门户中使用资源组。

## 创建资源组

每当在门户中创建资源时，始终会在资源组中创建该资源。你可以选择创建新的资源组，或者在创建流程中使用现有资源组。<br><br />

![创建资源组](./media/resource-group-portal/1_createWebsite.png)

当你创建由多个一同运行的资源（例如网站 + 数据库）组成的应用程序时，该应用程序始终会创建在其自身的资源组中，因此你可以使用资源组来管理所有相关资产的生命周期。随着应用程序的变化，你可以在资源组中添加或删除资源。

![创建资源组](./media/resource-group-portal/2_createWSandDB.png)

## 浏览资源组

你可以通过单击屏幕左侧的跳转条来浏览所有资源组。资源组提供了一个分页，其中显示了有关特定资源组的所有信息。另外，资源组分页还提供了资源组中所有资源的计费和监视信息的统一视图。

摘要部分显示资源组中所有资源的可视资源映射，以及与该资源组链接的其他资源组中的资源。资源映射还会显示每个资源的状态。
![资源组摘要](./media/resource-group-portal/3_1BrowseRGs.png)

你可以自定义资源映射部分，以放大视图，从而能够显示该资源组中包含的所有资源，以及链接的其他资源组中的资源。将此部分固定到启动板时，其内容就会复制到启动板。

![固定](./media/resource-group-portal/3_2BrowseRGs.png)

单击资源映射会启动该资源映射中所有资源的列表视图。此视图将列出某个资源组中或者与它链接的其他资源组中的所有资源。单击这些资源会启动其分页。

![查看资源](./media/resource-group-portal/3_3BrowseRGs.png)

## 将资源添加到资源组

你可以在资源组分页上使用“添加”命令将资源添加到资源组。遵照流程中的步骤可将其他资源添加到资源组。

![添加资源](./media/resource-group-portal/4_AddResource.png)

注意：建议你不要将团队项目放置在与其他 Azure 资源相同的资源组中。如果在新的帐户和组中创建团队项目，然后再创建网站，则站点组将默认为使用的最后一个组（VSO 组），并且运行时/开发人员资源最终将会放到同一个组中。

## 删除资源组

由于资源组可让你管理包含的所有资源的生命周期，因此，删除一个资源组会同时删除该组中包含的所有资源。你也可以删除资源组中的单个资源。在删除资源组时请多加小心，因为该资源组可能链接了其他资源。删除资源组时，你可以在资源映射中查看链接的资源，并采取必要的措施来避免出现任何意外的后果。

## 后续步骤
入门

- [Azure 资源管理器概述](resource-group-overview)  
- [将 Azure PowerShell 与 Azure 资源管理器配合使用](powershell-azure-resource-manager)
- [将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理配合使用](xplat-cli-azure-resource-manager)  

创建和部署应用程序

- [创作 Azure 资源管理器模板](resource-group-authoring-templates)  
- [使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy)  
- [Azure 中的资源组部署疑难解答](resource-group-deploy-debug)  
- [Azure 资源管理器模板函数](resource-group-template-functions)  
- [高级模板操作](resource-group-advanced-template)  
- [使用 .NET 库和模板部署 Azure 资源](arm-template-deployment)

组织资源

- [使用标记来组织 Azure 资源](resource-group-using-tags)  

管理和审核访问权限

- [管理和审核对资源的访问权限](resource-group-rbac)  
- [通过 Azure 资源管理器对服务主体进行身份验证](resource-group-authenticate-service-principal)  
- [使用 Azure 经典门户创建新的 Azure 服务主体](resource-group-create-service-principal-portal)  

<!---HONumber=61-->