---
title: 使用 PowerShell 建立彈性作業代理程式
description: 了解如何使用 PowerShell 建立彈性作業代理程式。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 9724e54b03e5de065b8b39cb57c6a9880cf37cc6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827198"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>使用 PowerShell 建立彈性作業代理程式

[彈性作業](sql-database-job-automation-overview.md#elastic-database-jobs-preview)可讓您以平行方式跨多個資料庫執行一或多個 Transact-SQL (T-SQL) 指令碼。

在本教學課程中，您將了解跨多個資料庫執行查詢所需的步驟：

> [!div class="checklist"]
> * 建立彈性工作代理程式
> * 建立作業認證，讓作業可在其目標上執行指令碼
> * 定義您要對其執行作業的目標 (伺服器、彈性集區、資料庫、分區對應)
> * 在目標資料庫中建立資料庫範圍認證，讓代理程式可連接及執行作業
> * 建立工作
> * 將作業步驟新增至作業
> * 開始執行作業
> * 監視作業

## <a name="prerequisites"></a>必要條件

升級版本的彈性資料庫作業具有一組新的 PowerShell Cmdlet，可以在移轉期間使用。 這些新的 Cmdlet 會將您所有的現有作業認證、目標 (包括資料庫、伺服器、自訂集合)、作業觸發程序、作業排程、作業內容及作業傳輸至新的彈性資料庫代理程式。

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>安裝最新的彈性作業 Cmdlet

如果您還沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

安裝 **Az.Sql** 1.1.1 預覽模組，以取得最新的彈性作業 Cmdlet。 在 PowerShell 中以系統管理存取權執行下列命令。

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

- 除了 **Az.Sql** 1.1.1 預覽模組以外，本教學課程也需要 *sqlserver* PowerShell 模組。 如需詳細資訊，請參閱[安裝 SQL Server PowerShell 模組](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module)。


## <a name="create-required-resources"></a>建立所需的資源

要建立彈性作業代理程式，必須要有作為[作業資料庫](sql-database-job-automation-overview.md#job-database)的資料庫 (S0 或更高版本)。 

*下列指令碼會建立新的資源群組、伺服器，以及作為作業資料庫的資料庫。下列指令碼也會建立含有兩個空白資料庫的第二個伺服器，以對其執行作業。*

彈性作業沒有特定的命名需求，因此，您可以使用您所需的任何命名慣例，只要它們符合 [Azure 需求](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)即可。

```powershell
# Sign in to your Azure account
Connect-AzAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>為您的訂用帳戶啟用彈性作業預覽版

若要使用彈性作業，請執行下列命令以在 Azure 訂用帳戶中註冊此功能。 針對要在其中佈建彈性作業代理程式的訂用帳戶執行此命令一次。 訂用帳戶若只包含屬於作業目標的資料庫就不需要註冊。

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>建立彈性作業代理程式

彈性作業代理程式是用來建立、執行和管理作業的 Azure 資源。 代理程式會根據排程來執行作業，或執行一次性的作業。

**New-AzSqlElasticJobAgent** Cmdlet 會要求必須已有 Azure SQL 資料庫存在，因此 *ResourceGroupName*、*ServerName* 和 *DatabaseName* 參數全都必須指向現有的資源。

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>建立作業認證，讓作業可在其目標上執行指令碼

作業在執行時會使用資料庫範圍認證連線至目標群組所指定的目標資料庫。 這些資料庫範圍認證也可用來連線至主要資料庫，以在伺服器或彈性集區中的資料庫作為目標群組成員類型時，將這些資料庫全數列舉。

資料庫範圍認證必須建立在作業資料庫中。  
所有目標資料庫都必須以足夠的權限登入，作業才能順利完成。

![彈性作業認證](media/elastic-jobs-overview/job-credentials.png)

除了影像中的認證以外，請留意在下列指令碼中新增的 *GRANT* 命令。 我們為此範例作業選擇的指令碼需要這些權限。 由於此範例會在目標資料庫中建立新的資料表，因此每個目標資料庫都必須具備適當的權限才能成功執行。

若要建立必要的作業認證 (在作業資料庫中)，請執行下列指令碼：

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>定義您要對其執行作業的目標資料庫

[目標群組](sql-database-job-automation-overview.md#target-group)可定義一或多個將會執行作業步驟的資料庫。 

下列程式碼片段會建立兩個目標群組：*ServerGroup* 和 *ServerGroupExcludingDb2*。 *ServerGroup* 會以執行時存在於伺服器上的所有資料庫為目標，*ServerGroupExcludingDb2* 則會以伺服器上的所有資料庫為目標，但 *TargetDb2* 除外：

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>建立工作

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>建立作業步驟

此範例會為要執行的作業定義兩個作業步驟。 第一個作業步驟 (*步驟 1*) 會在目標群組 *ServerGroup* 的每個資料庫中 建立新的資料表 (*Step1Table*)。 第二個作業步驟 (*步驟 2*) 會在每個資料庫中建立新的資料表 (*Step2Table*)，但 *TargetDb2* 除外，因為[先前定義的](#define-the-target-databases-you-want-to-run-the-job-against)目標群組指定要加以排除。

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>執行工作

若要立即啟動作業，請執行下列命令：

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzSqlElasticJob
$JobExecution
```

順利完成之後，您應該會在 TargetDb1 中看到兩個新的資料表，而 TargetDb2 中只有一個新的資料表：


   ![SSMS 中的新資料表驗證](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>監視作業執行狀態

下列程式碼片段會取得作業執行詳細資料：

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

### <a name="job-execution-states"></a>作業執行狀態

下表列出可能的作業執行狀態：

|State|說明|
|:---|:---|
|**建立時間** | 作業執行剛建立，且尚未開始執行。|
|**InProgress** | 作業執行目前正在進行中。|
|**WaitingForRetry** | 作業執行無法完成其動作，且正在等候重試。|
|**已成功** | 作業執行已順利完成。|
|**SucceededWithSkipped** | 作業執行已順利完成，但略過了部分子系。|
|**已失敗** | 作業執行失敗，且用完重試次數。|
|**TimedOut** | 作業執行逾時。|
|**Canceled** | 作業執行已取消。|
|**已略過** | 已略過作業執行，因為已在相同的目標上執行相同作業步驟的另一個執行。|
|**WaitingForChildJobExecutions** | 作業執行正在等候其子系執行完成。|

## <a name="schedule-the-job-to-run-later"></a>排程要稍後執行的作業

若要將作業排程在特定時間執行，請執行下列命令：

```powershell
# Run every hour starting from now
$Job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>清除資源

您可以刪除資源群組，以刪除在本教學課程中建立的 Azure 資源。

> [!TIP]
> 如果您打算繼續使用這些作業，請勿清除在此本文中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除在本文中建立的所有資源。
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已對一組資料庫執行 Transact-SQL 指令碼。  您已了解如何執行下列工作：

> [!div class="checklist"]
> * 建立彈性工作代理程式
> * 建立作業認證，讓作業可在其目標上執行指令碼
> * 定義您要對其執行作業的目標 (伺服器、彈性集區、資料庫、分區對應)
> * 在目標資料庫中建立資料庫範圍認證，讓代理程式可連接及執行作業
> * 建立工作
> * 將作業步驟新增至作業
> * 開始執行作業
> * 監視作業

> [!div class="nextstepaction"]
>[使用 Transact-SQL 管理彈性作業](elastic-jobs-tsql.md)
