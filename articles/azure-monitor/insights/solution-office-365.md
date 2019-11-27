---
title: Azure 中的 Office 365 管理解決方案 | Microsoft Docs
description: 本文提供在 Azure 中設定和使用 Office 365 解決方案的相關詳細資料。  它包含在 Azure 監視器中所建立之 Office 365 記錄的詳細描述。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: aff6be1a6abf2550013b752ba4f796ffe255499f
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539054"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure 中的 Office 365 管理解決方案 (預覽)

![Office 365 標誌](media/solution-office-365/icon.png)


> [!NOTE]
> 安裝和設定 Office 365 解決方案的建議方法是在[Azure Sentinel](../../sentinel/overview.md)中啟用[Office 365 連接器](../../sentinel/connect-office-365.md)，而不是使用本文中的步驟。 這是 Office 365 解決方案的更新版本，具有改良的設定體驗。 若要連接 Azure AD 記錄，您可以使用 [ [Azure Sentinel Azure AD 連接器](../../sentinel/connect-azure-active-directory.md)] 或 [[設定 Azure AD 診斷設定](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)]，以提供比 Office 365 記錄管理更豐富的記錄資料。 
>
> 當您上[架 Azure Sentinel](../../sentinel/quickstart-onboard.md)時，請指定您想要安裝 Office 365 解決方案的 Log Analytics 工作區。 一旦您啟用連接器，解決方案就可以在工作區中使用，而且會與您已安裝的任何其他監視解決方案完全相同。
>
> Azure 政府機構雲端的使用者必須使用這篇文章中的步驟來安裝 Office 365，因為政府雲端尚未提供 Azure Sentinel。

Office 365 管理解決方案可讓您監視 Azure 監視器中的 Office 365 環境。

- 監視 Office 365 帳戶上的使用者活動，以分析使用模式並識別行為趨勢。 例如，您可以擷取特定使用方式情節，例如在貴組織外部共用的檔案或最熱門的 SharePoint 網站。
- 監視系統管理員活動以追蹤設定變更或高權限作業。
- 偵測並調查的不必要的使用者行為，並可以針對貴組織的需求進行自訂。
- 示範稽核與合規性。 例如，您可以監視機密檔案的檔案存取作業，以協助進行稽核與合規性流程。
- 針對組織的 Office 365 活動資料使用[記錄查詢](../log-query/log-query-overview.md)，執行作業疑難排解。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件

安裝和設定此解決方案之前必須先具備下列項目。

- 組織的 Office 365 訂閱。
- 全域管理員的使用者帳戶認證。
- 若要接收稽核資料，您必須在 Office 365 訂閱中[設定稽核](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)。  請注意，[信箱稽核](https://technet.microsoft.com/library/dn879651.aspx) \(機器翻譯\) 需要另行設定。  如果未設定稽核，您仍可安裝解決方案並收集其他資料。
 

## <a name="management-packs"></a>管理組件

此解決方案不會在[已連線的管理群組](../platform/om-agents.md)中安裝任何管理組件。
  

## <a name="install-and-configure"></a>安裝及設定

一開始先新增 [Office 365 解決方案到您的訂用帳戶](solutions.md#install-a-monitoring-solution)。 新增之後，您必須執行本節中的設定步驟，讓解決方案存取您的 Office 365 訂用帳戶。

### <a name="required-information"></a>必要資訊

開始此程序前，請先收集下列資訊。

從您的 Log Analytics 工作區：

- 工作區名稱：將從中收集 Office 365 資料的工作區。
- 資源群組名稱：包含工作區的資源群組。
- Azure 訂用帳戶識別碼：包含工作區的訂用帳戶。

從您的 Office 365 訂用帳戶：

- 使用者名稱：系統管理帳戶的電子郵件地址。
- 租用戶識別碼：Office 365 訂用帳戶的唯一識別碼。

在 Azure Active Directory 中建立和設定 Office 365 應用程式期間，應該會收集下列資訊：

- 應用程式（用戶端）識別碼：16個字元的字串，代表 Office 365 用戶端。
- 用戶端密碼：驗證所需的加密字串。

### <a name="create-an-office-365-application-in-azure-active-directory"></a>在 Azure Active Directory 中建立 Office 365 應用程式

第一個步驟在 Azure Active Directory 中建立應用程式，讓管理解決方案用來存取 Office 365 解決方案。

1. 在 [https://portal.azure.com](https://portal.azure.com/) 上登入 Azure 入口網站。
1. 選取 [Azure Active Directory]，然後選取 [應用程式註冊]。
1. 按一下 [**新增註冊**]。

    ![新增應用程式註冊](media/solution-office-365/add-app-registration.png)
1. 輸入應用程式**名稱**。 針對**支援的帳戶類型**，選取**任何組織目錄（任何 Azure AD 目錄-** 多租使用者）中的帳戶。
    
    ![建立應用程式](media/solution-office-365/create-application.png)
1. 按一下 [**註冊**並驗證應用程式資訊]。

    ![註冊的應用程式](media/solution-office-365/registered-app.png)

1. 儲存應用程式（用戶端）識別碼，以及先前收集到的其餘資訊。


### <a name="configure-application-for-office-365"></a>設定 Office 365 的應用程式

1. 選取 [**驗證**]，並確認已在 [**支援的帳戶類型**] 下選取**任何組織目錄（任何 Azure AD 目錄-** 多租使用者）中的帳戶。

    ![設定多租用戶](media/solution-office-365/settings-multitenant.png)

1. 選取 [ **API 許可權**]，然後按一下 [**新增許可權**]。
1. 按一下 [ **Office 365 管理 api**]。 

    ![選取 API](media/solution-office-365/select-api.png)

1. 在**您的應用程式需要何種許可權類型的情況**下，請針對**應用程式許可權**和**委派的許可權**選取下列選項：
   - 讀取您組織的服務健康情況資訊
   - 讀取您組織的活動資料
   - 讀取您組織的活動報告

     ![選取 API](media/solution-office-365/select-permissions-01.png)![選取 API](media/solution-office-365/select-permissions-02.png)

1. 按一下 [新增權限]。
1. 按一下 **[授與管理員同意**]，然後在系統要求驗證時按一下 **[是]** 。


### <a name="add-a-secret-for-the-application"></a>為應用程式新增秘密

1. 選取 [**憑證 & 密碼**]，然後選取 [**新增用戶端密碼**]。

    ![之間的信任](media/solution-office-365/secret.png)
 
1. 輸入新金鑰的 [描述] 和 [持續時間]。
1. 按一下 [**新增**]，然後將產生的**值**儲存為用戶端密碼，以及先前收集到的其餘資訊。

    ![之間的信任](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>新增管理員同意

初次啟用系統管理帳戶時，您必須提供應用程式的管理員同意。 您可以利用 PowerShell 指令碼來完成此作業。 

1. 將下列指令碼儲存為 office365_consent.ps1。

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. 使用下列命令來執行指令碼。 系統會提示您提供認證兩次。 請先提供 Log Analytics 工作區的認證，然後提供 Office 365 租用戶的全域管理員認證。

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    範例：

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. 您會看到類似下圖的視窗。 按一下 [接受]。
    
    ![系統管理員同意](media/solution-office-365/admin-consent.png)

> [!NOTE]
> 您可能會被重新導向至不存在的頁面。 請將它視為成功。

### <a name="subscribe-to-log-analytics-workspace"></a>訂閱 Log Analytics 工作區

最後一個步驟將應用程式訂閱到您的 Log Analytics 工作區。 您也可以利用 PowerShell 指令碼來完成此作業。

1. 將下列指令碼儲存為 office365_subscription.ps1。

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }

    Function RESTAPI-Auth { 
    $global:SubscriptionID = $Subscription.Subscription.Id
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()

    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. 使用下列命令來執行指令碼：

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    範例：

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>疑難排解

如果您的應用程式已訂閱此工作區，或此租用戶訂閱了另一個工作區，您可能會看到下列錯誤。

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

如果提供了無效參數值，您可能會看到下列錯誤。

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>解除安裝

您可以使用[移除管理解決方案](solutions.md#remove-a-monitoring-solution)中的程序移除 Office 365 管理解決方案。 但這不會停止從 Office 365 收集資料到 Azure 監視器。 請遵循底下程序，從 Office 365 取消訂閱並停止收集資料。

1. 將下列指令碼儲存為 office365_unsubscribe.ps1。

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. 使用下列命令來執行指令碼：

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    範例：

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

系統會提示您輸入認證。 提供 Log Analytics 工作區的認證。

## <a name="data-collection"></a>資料收集

### <a name="supported-agents"></a>支援的代理程式

Office 365 解決方案不會從任何 [Log Analytics 代理程式](../platform/agent-data-sources.md)擷取資料。  它會直接從 Office 365 擷取資料。

### <a name="collection-frequency"></a>收集頻率

一開始收集資料可能會需要幾個小時。 一旦開始收集資料，每次建立一筆記錄時，Office 365 都會將 [Webhook 通知](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) \(英文\) 連同詳細資料傳送至 Azure 監視器。 收到此記錄的幾分鐘內，即可將其用於 Azure 監視器。

## <a name="using-the-solution"></a>使用方案

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

當您將 Office 365 解決方案新增至 Log Analytics 工作區時，[Office 365] 圖格會新增至儀表板。 此圖格會顯示計數並以圖形表示環境中的電腦數目及其更新合規性。<br><br>
![Office 365 摘要圖格](media/solution-office-365/tile.png)  

按一下 [Office 365] 圖格以開啟 [Office 365] 儀表板。

![Office 365 儀表板](media/solution-office-365/dashboard.png)  

此儀表板包含下表中的資料行。 每個資料行依計數列出前十個警示，這幾個警示符合該資料行中指定範圍和時間範圍的準則。 您可以按一下資料行底部的 [查看全部]，或按一下資料行標頭，以執行記錄搜尋來提供完整清單。

| 資料欄 | 描述 |
|:--|:--|
| 作業 | 提供所有受監視 Office 365 訂閱中之作用中使用者的相關資訊。 您也可以查看一段時間內發生的活動數。
| Exchange | 顯示 Exchange Server 活動細目，例如 Add-Mailbox 權限或 Set-Mailbox。 |
| SharePoint | 顯示使用者最常對 SharePoint 文件執行的活動。 當您從此圖格向下鑽研時，搜尋頁面會顯示這些活動的詳細資料，例如此活動的目標文件與位置。 例如，針對「已存取檔案」事件，您可以查看所存取的文件、其相關帳戶名稱，以及 IP 位址。 |
| Azure Active Directory | 包含熱門使用者活動，例如「重設使用者密碼」和「登入嘗試」。 向下鑽研時，您可以像「結果狀態」一般地查看這些活動的詳細資料。 如果您想要監視 Azure Active Directory 上的可疑活動，這會很有幫助。 |




## <a name="azure-monitor-log-records"></a>Azure 監視器記錄

由 Office 365 解決方案在 Azure 監視器 Log Analytics 工作區中建立的所有記錄，都具有 **OfficeActivity** 的「類型」。  **OfficeWorkload** 屬性可決定該記錄所指的 Office 365 服務：Exchange、AzureActiveDirectory、SharePoint 或 OneDrive。  **RecordType** 屬性指定作業的類型。  每種作業類型會有不同的屬性，如下表所示。

### <a name="common-properties"></a>通用屬性

以下是所有 Office 365 記錄通用的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| 在系統提示您進行確認時，輸入 | *OfficeActivity* |
| ClientIP | 記錄活動時所使用之裝置的 IP 位址。 IP 位址會以 IPv4 或 IPv6 位址格式顯示。 |
| OfficeWorkload | 記錄所指的 Office 365 服務。<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| 作業 | 使用者或管理員活動的名稱。  |
| OrganizationId | 貴組織的 Office 365 租用戶 GUID。 無論此值是在哪一個 Office 365 服務中發生，對於貴組織而言它將會一律相同。 |
| RecordType | 執行的作業類型。 |
| ResultStatus | 指出 (Operation 屬性中指定的) 動作是否成功。 可能的值為 Succeeded、PartiallySucceeded 或 Failed。 對於 Exchange 管理員活動，這個值將會是 True 或 False。 |
| UserId | 執行動作導致記下該記錄之使用者的 UPN (使用者主體名稱)，例如 my_name@my_domain_name。 請注意，由系統帳戶 (例如 SHAREPOINT\system 或 NTAUTHORITY\SYSTEM) 所執行之活動的記錄也會包含在內。 | 
| UserKey | UserId 屬性所識別之使用者的替代識別碼。  例如，針對由使用者在 SharePoint、商務用 OneDrive 及 Exchange 中所執行的事件，此屬性都會填入 Passport 唯一識別碼 (PUID)。 針對在其他服務中所發生的事件，以及由系統帳戶所執行的事件，此屬性也可能會將相同的值指定為 UserID 屬性|
| UserType | 執行作業的使用者類型。<br><br>Admin<br>應用程式<br>DcAdmin<br>定期<br>Reserved<br>ServicePrincipal<br>系統 |


### <a name="azure-active-directory-base"></a>Azure Active Directory 基底

以下是所有 Azure Active Directory 記錄通用的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD 事件類型。 |
| ExtendedProperties | Azure AD 事件的擴充屬性。 |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory 帳戶登入

這些記錄會在 Active Directory 使用者嘗試登入時建立。

| 屬性 | 描述 |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | 觸發帳戶登入事件的應用程式，例如 Office 15。 |
| `Client` | 帳戶登入事件所使用的用戶端裝置、裝置作業系統及裝置瀏覽器的相關詳細資料。 |
| `LoginStatus` | 此屬性直接來自 OrgIdLogon.LoginStatus。 各種感興趣之登入失敗的對應可由警示演算法完成。 |
| `UserDomain` | 租用戶身分識別資訊 (TII)。 | 


### <a name="azure-active-directory"></a>Azure Active Directory

這些記錄會在針對 Azure Active Directory 物件進行變更或新增時建立。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | 對之執行 (透過 Operation 屬性所識別的) 動作的使用者。 |
| 執行者 | 執行動作的使用者或服務主體。 |
| ActorContextId | 執行者所屬組織的 GUID。 |
| ActorIpAddress | 以 IPV4 或 IPV6 位址格式顯示的執行者 IP 位址。 |
| InterSystemsId | 在 Office 365 服務內跨元件追蹤動作的 GUID。 |
| IntraSystemId |   由 Azure Active Directory 產生來追蹤動作的 GUID。 |
| SupportTicketId | 在「代表採取動作」狀況下進行之動作的客戶支援票證識別碼。 |
| TargetContextId | 目標使用者所屬組織的 GUID。 |


### <a name="data-center-security"></a>資料中心安全性

這些記錄是從資料中心安全性稽核資料所建立。  

| 屬性 | 描述 |
|:--- |:--- |
| EffectiveOrganization | 提高權限/Cmdlet 的目標租用戶名稱。 |
| ElevationApprovedTime | 核准提高權限時的時間戳記。 |
| ElevationApprover | Microsoft 管理員的名稱。 |
| ElevationDuration | 提高權限作用中的持續時間。 |
| ElevationRequestId |  提高權限要求的唯一識別碼。 |
| ElevationRole | 要求提高權限的角色。 |
| ElevationTime | 提高權限的開始時間。 |
| Start_Time | Cmdlet 開始執行的時間。 |


### <a name="exchange-admin"></a>Exchange 管理員

這些記錄會在對 Exchange 設定做出變更時建立。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  指定 Cmdlet 是由貴組織的使用者、由 Microsoft 資料中心人員或資料中心服務帳戶，還是由委派的系統管理員執行。 False 值表示 Cmdlet 是由貴組織的人員執行。 True 值表示 Cmdlet 是由資料中心人員、資料中心服務帳戶或是委派的系統管理員執行。 |
| ModifiedObjectResolvedName |  這是 Cmdlet 所修改之物件的使用者易記名稱。 這只有在 Cmdlet 修改物件時才會記錄。 |
| OrganizationName | 租用戶的名稱。 |
| OriginatingServer | 執行 Cmdlet 之伺服器的名稱。 |
| parameters | 搭配在 Operations 屬性中所識別的 Cmdlet 所使用之所有參數的名稱與值。 |


### <a name="exchange-mailbox"></a>Exchange 信箱

這些記錄會在針對 Exchange 信箱做出變更或新增時建立。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | 用以執行作業的電子郵件用戶端相關資訊，例如瀏覽器版本、Outlook 版本及行動裝置資訊。 |
| Client_IPAddress | 記錄作業時所使用之裝置的 IP 位址。 IP 位址會以 IPv4 或 IPv6 位址格式顯示。 |
| ClientMachineName | 裝載 Outlook 用戶端的機器名稱。 |
| ClientProcessName | 用來存取信箱的電子郵件用戶端。 |
| ClientVersion | 電子郵件用戶端的版本。 |
| InternalLogonType | 保留供內部使用。 |
| Logon_Type | 指出存取信箱並執行所記錄之作業的使用者類型。 |
| LogonUserDisplayName |    執行作業之使用者的使用者易記名稱。 |
| LogonUserSid | 執行作業之使用者的 SID。 |
| MailboxGuid | 被存取之信箱的 Exchange GUID。 |
| MailboxOwnerMasterAccountSid | 信箱擁有者帳戶的主要帳戶 SID。 |
| MailboxOwnerSid | 信箱擁有者的 SID。 |
| MailboxOwnerUPN | 擁有被存取信箱之人員的電子郵件地址。 |


### <a name="exchange-mailbox-audit"></a>Exchange 信箱稽核

這些記錄會在建立信箱稽核項目時建立。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| item | 表示對之執行作業的項目 | 
| SendAsUserMailboxGuid | 存取以傳送電子郵件之信箱的 Exchange GUID。 |
| SendAsUserSmtp | 被模擬之使用者的 SMTP 位址。 |
| SendonBehalfOfUserMailboxGuid | 存取以代為傳送電子郵件之信箱的 Exchange GUID。 |
| SendOnBehalfOfUserSmtp | 代表其傳送電子郵件之使用者的 SMTP 位址。 |


### <a name="exchange-mailbox-audit-group"></a>Exchange 信箱稽核群組

這些記錄會在針對 Exchange 群組做出變更或新增時建立。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | 群組中每個項目的相關資訊。 |
| CrossMailboxOperations | 指出該作業是否涉及多個信箱。 |
| DestMailboxId | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 指定目標信箱 GUID。 |
| DestMailboxOwnerMasterAccountSid | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 針對目標信箱擁有者的主要帳戶 SID 指定 SID。 |
| DestMailboxOwnerSid | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 指定目標信箱的 SID。 |
| DestMailboxOwnerUPN | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 指定目標信箱之擁有者的 UPN。 |
| DestFolder | 針對移動等作業的目的地資料夾。 |
| 資料夾 | 項目群組所在的資料夾。 |
| 資料夾 |     涉及作業之來源資料夾的相關資訊，例如，在選取資料夾後將它加以刪除。 |


### <a name="sharepoint-base"></a>SharePoint 基底

這些是所有 SharePoint 記錄通用的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | 識別事件在 SharePoint 中發生。 可能的值為 SharePoint 或 ObjectModel。 |
| ItemType | 被存取或修改之物件的類型。 如需物件類型的詳細資料，請參閱 ItemType 表格。 |
| MachineDomainInfo | 裝置同步作業的相關資訊。 此資訊只會在存在於要求中時才會報告。 |
| MachineId |   裝置同步作業的相關資訊。 此資訊只會在存在於要求中時才會報告。 |
| Site_ | 使用者存取的檔案或資料夾所在之網站的 GUID。 |
| Source_Name | 觸發已稽核作業的實體。 可能的值為 SharePoint 或 ObjectModel。 |
| UserAgent | 使用者的用戶端或瀏覽器的相關資訊。 此資訊是由用戶端或瀏覽器所提供。 |


### <a name="sharepoint-schema"></a>SharePoint 結構描述

這些記錄會在針對 SharePoint 做出設定變更時建立。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | 自訂事件的選擇性字串。 |
| Event_Data |  自訂事件的選擇性承載。 |
| ModifiedProperties | 系統會針對管理員事件 (例如，將使用者新增為網站或網站集合管理員群組的成員) 包含此屬性。 此屬性包含被修改之屬性的名稱 (例如「網站管理員」群組)、已修改屬性的新值 (例如新增為網站管理員的使用者)，以及已修改物件先前的值。 |


### <a name="sharepoint-file-operations"></a>SharePoint 檔案作業

這些記錄是為了回應 SharePoint 中的檔案作業而建立。

| 屬性 | 描述 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | 被複製或移動之檔案的副檔名。 此屬性僅針對 FileCopied 和 FileMoved 事件顯示。 |
| DestinationFileName | 被複製或移動之檔案的名稱。 此屬性僅針對 FileCopied 和 FileMoved 事件顯示。 |
| DestinationRelativeUrl | 複製或移動檔案之目的地資料夾的 URL。 SiteURL、DestinationRelativeURL 及 DestinationFileName 參數之值的組合，和 ObjectID 屬性的值相同，也就是被複製之檔案的完整路徑名稱。 此屬性僅針對 FileCopied 和 FileMoved 事件顯示。 |
| SharingType | 指派給與之共用資源之使用者的共用權限類型。 此使用者是由 UserSharedWith 參數識別。 |
| Site_Url | 使用者所存取之檔案或資料夾所在的網站 URL。 |
| SourceFileExtension | 使用者所存取之檔案的副檔名。 如果存取的物件是資料夾，此屬性將會是空白。 |
| SourceFileName |  使用者所存取之檔案或資料夾的名稱。 |
| SourceRelativeUrl | 包含使用者所存取之檔案的資料夾 URL。 SiteURL、SourceRelativeURL 及 SourceFileName 參數之值的組合，和 ObjectID 屬性的值相同，也就是使用者所存取之檔案的完整路徑名稱。 |
| UserSharedWith |  與之共用資源的使用者。 |




## <a name="sample-log-searches"></a>記錄檔搜尋範例

下表提供此方案所收集之更新記錄的記錄搜尋範例。

| 查詢 | 描述 |
| --- | --- |
|Office 365 訂閱上所有作業的計數 |OfficeActivity &#124; summarize count() by Operation |
|SharePoint 網站的使用情況|OfficeActivity &#124; where OfficeWorkload = ~ "sharepoint" &#124;摘要 Count （） by SiteUrl \| 排序依據計數 asc|
|依使用者類型分類的檔案存取作業|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|使用特定關鍵字進行搜尋|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|監視 Exchange 上的外部動作|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>後續步驟

* 使用 [Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)來檢視詳細的更新資料。
* [建立自己的儀表板](../learn/tutorial-logs-dashboards.md)來顯示您最愛的 Office 365 搜尋查詢。
* [建立警示](../platform/alerts-overview.md)來讓系統主動通知您重要的 Office 365 活動。  
