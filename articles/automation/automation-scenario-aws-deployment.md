<properties
   pageTitle="在 Amazon Web Services 中自动部署 VM | Azure"
   description="本文演示如何使用 Azure 自动化来自动创建 Amazon Web Service VM"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="" />
<tags
   ms.service="automation"
   ms.date="04/13/2016"
   wacn.date="05/13/2016" />

# Azure 自动化解决方案 - 预配 AWS 虚拟机 

在本文中，我们将演示如何使用 Azure 自动化在 Amazon Web Service (AWS) 订阅中预配虚拟机，并为该 VM 提供特定的名称（AWS 称为“标记”VM）。

## 先决条件

在本文中，需要用到 Azure 自动化帐户和 AWS 订阅。有关设置 Azure 自动化帐户和使用 AWS 订阅凭据对它进行设置的详细信息，请参阅 [Configure Authentication with Amazon Web Services（使用 Amazon Web Services 配置身份验证）](/documentation/articles/automation-sec-configure-aws-account)。继续之前，应该使用 AWS 订阅凭据来创建或更新此帐户，因为我们将在下面的步骤中引用此帐户。


## 部署 Amazon Web Services PowerShell 模块

我们的 VM 预配 Runbook 将利用 AWS PowerShell 模块来执行其功能。执行以下步骤，将该模块添加到使用 AWS 订阅凭据配置的自动化帐户。

1. 打开 Web 浏览器并导航到 [PowerShell 库](http://www.powershellgallery.com/packages/AWSPowerShell)，然后单击“部署到 Azure 自动化”按钮。<br>![AWS PS 模块导入](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)

2. 你将转到 Azure 登录页，身份验证后，将路由到 Azure 门户，其中显示了以下边栏选项卡。<br>![导入模块边栏选项卡](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)

3. 从“资源组”下拉列表中选择资源组，并在“参数”边栏选项卡上提供以下信息：
   * 从“新的或现有的自动化帐户(字符串)”下拉列表中，选择“现有”。  
   * 在“自动化帐户名称(字符串)”框中，键入包含 AWS 订阅凭据的确切自动化帐户名称。例如，如果已创建名为 **AWSAutomation** 的专用帐户，请在此框中键入该名称。
   * 从“自动化帐户位置”下拉列表中选择适当的区域。

4. 输入完所需的信息后，单击“创建”。

    >[AZURE.NOTE] 将 PowerShell 模块导入 Azure 自动化时会同时提取 cmdlet，在模块完全完成导入并提取 cmdlet 之前不会出现这些活动。此过程可能需要几分钟时间。
<br>
5. 在 Azure 门户中，打开步骤 3 中引用的自动化帐户。
6. 单击“资产”磁贴，然后在“资产”边栏选项卡上选择“模块”磁贴。
7. 在“模块”边栏选项卡上，你将在列表中看到 **AWSPowerShell** 模块。

## 创建 AWS 部署 VM Runbook

部署 AWS PowerShell 模块后，可以创作一个 Runbook，以使用 PowerShell 脚本在 AWS 中自动预配虚拟机。以下步骤演示如何在 Azure 自动化中使用本机 PowerShell 脚本。

>[AZURE.NOTE] 有关其他选项以及此脚本的信息，请访问 [PowerShell 库](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript)。


1. 通过打开 PowerShell 会话并键入以下命令，从 PowerShell 库下载 PowerShell 脚本 New-AwsVM：<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
<br>
2. 从 Azure 门户打开你的自动化帐户，然后单击“Runbook”磁贴。  
3. 在“Runbook”边栏选项卡中，选择“添加 Runbook”。
4. 在“添加 Runbook”边栏选项卡中，选择“快速创建”（创建新 Runbook）。
5. 在“Runbook”属性边栏选项卡上的“名称”框中键入 Runbook 的名称，从“Runbook 类型”下拉列表中选择“PowerShell”，然后单击“创建”。<br>![导入模块边栏选项卡](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. 出现“编辑 PowerShell Runbook”边栏选项卡时，请将 PowerShell 脚本复制并粘贴到 Runbook 制作画布中。<br>![Runbook PowerShell 脚本](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>

    >[AZURE.NOTE] 使用示例 PowerShell 脚本时，请注意以下事项：
    >
    > - Runbook 包含一些默认参数值。请评估所有默认值，并根据需要进行更新。
    > - 如果已将 AWS 凭据存储为名称与 **AWScred** 不同的凭据资产，则需要更新脚本的第 57 行，以使其匹配。  
    > - 在 PowerShell 中使用 AWS CLI 命令（特别是使用此示例 Runbook）时，必须指定 AWS 区域。否则，cmdlet 将会失败。有关更多详细信息，请查看 AWS Tools for PowerShell 文档中的 AWS 主题 [Specify AWS Region（指定 AWS 区域）](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)。
<br>
7. 若要从 AWS 订阅检索映像名称列表，请启动 PowerShell ISE 并导入 AWS PowerShell 模块。通过将 ISE 环境中的 **Get-AutomationPSCredential** 替换为 **AWScred = Get-Credential**，向 AWS 进行身份验证。此时系统会提示你输入凭据，你可以提供“访问密钥 ID”作为用户名，提供“机密访问密钥”作为密码。请参阅以下示例：

		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password

		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion

		Get-EC2ImageByName -ProfileName AWSProfile
   将返回以下输出：<br>
   ![获取 AWS 映像](./media/automation-scenario-aws-deployment/powershell-ise-output.png)  
8. 在自动化变量中复制并粘贴一个映像名称，如 Runbook 中作为 **$InstanceType** 所引用的那样。由于我们在本示例中使用免费的 AWS 层订阅，因此将使用 **t2.micro** 作为 Runbook 示例。
9. 保存 Runbook，单击“发布”以发布该 Runbook，然后在出现提示时单击“是”。


### 测试 AWS VM Runbook
在继续测试 Runbook 之前，需要确认一些事项。具体而言：

   -  用于向 AWS 进行身份验证的资产已创建且命名为 **AWScred**，或脚本已更新为引用你的凭据资产的名称。  
   -  已在 Azure 自动化中导入 AWS PowerShell 模块
   -  已创建新的 Runbook 并根据需要验证和更新了参数值
   -  Runbook 设置“日志记录和跟踪”下的“记录详细记录”和可选的“记录进度记录”已设置为“打开”。<br>![Runbook 日志记录和跟踪](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)

1. 我们需要启动 Runbook，因此请单击“启动”，然后在“启动 Runbook”边栏选项卡打开时单击“确定”即可。
2. 在“启动 Runbook”边栏选项卡上提供 **VMname**。接受前面在脚本中预配置的其他参数的默认值。单击“确定”启动 Runbook 作业。<br>![启动 New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 会为我们刚刚创建的 Runbook 作业打开作业窗格。关闭此窗格。
4. 从 Runbook 作业边栏选项卡中选择“所有日志”磁贴，即可查看作业的进度和输出**流**。<br>![流输出](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. 若要确认是否正在预配 VM，请登录到 AWS 管理控制台（如果当前尚未登录）。<br>![AWS 控制台部署的 VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## 后续步骤
-	若要开始使用 PowerShell 工作流 Runbook，请参阅 [My first PowerShell workflow runbook（我的第一个 PowerShell 工作流 Runbook）](/documentation/articles/automation-first-runbook-textual)
-	有关 PowerShell 脚本支持功能的详细信息，请参阅 [Native PowerShell script support in Azure Automation（Azure 自动化中的本机 PowerShell 脚本支持）](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2)

<!---HONumber=Mooncake_0516_2016-->