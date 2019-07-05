---
title: Azure Active Directory Domain Services：限域同步處理 | Microsoft Docs
description: 設定從 Azure AD 到受控網域的限域同步處理
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: bb96e7ccbc38a71d24178c31f8eb9a077c06b8f0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472532"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>設定從 Azure AD 到受控網域的限域同步處理
此文章說明如何設定只將特定使用者帳戶從 Azure AD 目錄同步至 Azure AD Domain Services 受控網域。


## <a name="group-based-scoped-synchronization"></a>群組型限域同步處理
預設會將您 Azure AD 目錄內的所有使用者和群組都同步至您的受控網域。 如果只有幾個使用者使用受控網域，您可以只同步這些使用者帳戶。 群組型限域同步處理便可讓您這麼做。 設定好之後，便只會將屬於您所指定群組的使用者帳戶同步至受控網域。

下表可協助您判斷如何使用限域同步處理：

| **目前狀態** | **所需狀態** | **必要設定** |
| --- | --- | --- |
| 您的現有受控網域已設定成同步所有使用者帳戶和群組。 | 您想要只將屬於特定群組的使用者帳戶同步至受控網域。 | [刪除現有的受控網域](delete-aadds.md)。 接著，依照此文章中的指示，重新建立它並設定限域同步處理。 |
| 您沒有現有的受控網域。 | 您想要建立新的受控網域，並只同步屬於特定群組的使用者帳戶。 | 依照此文章中的指示，建立新的受控網域並設定限域同步處理。 |
| 您的現有受控網域已設定成只同步屬於特定群組的帳戶。 | 您想要修改應將所含使用者同步至受控網域的群組清單。 | 依照此文章中的指示來修改限域同步處理。 |

> [!WARNING]
> **變更同步處理範圍會導致您的受控網域進行重新同步處理。**
> 
>  * 當您變更受控網域的同步處理範圍時，會進行完整重新同步處理。
>  * 受控網域中不再需要的物件會被刪除。 系統會在受控網域中建立新物件。
>  * 依據受控網域及 Azure AD 目錄中的物件 (使用者、群組及群組成員資格) 數目而定，重新同步處理可能需要相當長的時間才能完成。 針對有數百個物件的大型目錄，重新同步處理可能需要幾天的時間。


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>使用 Azure 入口網站，建立新的受控網域並啟用群組型限域同步處理

1. 遵循[使用者入門指南](create-instance.md)，以建立受控網域。
2. 在 Azure AD Domain Services 建立精靈的同步處理樣式選取範圍期間，選擇 [限域]  。

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>使用 PowerShell，建立新的受控網域並啟用群組型限域同步處理
請使用 PowerShell 來完成這組步驟。 請參考指示以[使用 PowerShell 啟用 Azure Active Directory Domain Services](powershell-create-instance.md)。 此文章中的一些步驟已經過些微修改來設定限域同步處理。

請完成下列步驟來設定對受控網域的群組型限域同步處理：

1. 完成下列工作：
   * [工作 1：安裝必要的 PowerShell 模組](powershell-create-instance.md#task-1-install-the-required-powershell-modules)。
   * [工作 2：在 Azure AD 目錄中建立必要的服務主體](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)。
   * [工作 3:建立並設定 ' AAD DC Administrators' group]powershell-create-instance.md#task-3-create-and-configure-the-aad-dc-administrators-group。）
   * [工作 4：註冊 Azure AD Domain Services 資源提供者](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)。
   * [工作 5：建立資源群組](powershell-create-instance.md#task-5-create-a-resource-group)。
   * [工作 6：建立和設定虛擬網路](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)。

2. 選取您想要同步的群組，並提供您想要同步至受控網域之群組的顯示名稱。

3. 將[下一節中的指令碼](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)儲存至名為 ```Select-GroupsToSync.ps1``` 的檔案中。 執行類似下方的指令碼：

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **請務必包含 'AAD DC Administrators' 群組。**
   >
   > 您必須將 'AAD DC Administrators' 群組包含在為限域同步處理設定的群組清單中。 如果未包含此群組，受控網域將無法供使用。
   >

4. 現在，建立受控網域，並為受控網域啟用群組型限域同步處理。 請在 ```Properties``` 參數中包含 ```"filteredSync" = "Enabled"``` 屬性。 例如，查看下列複製自[工作 7：佈建 Azure AD Domain Services 受控網域](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)的指令碼片段。

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > 請務必將 ```"filteredSync" = "Enabled"``` 包含在 ```-Properties``` 參數中，如此才能為受控網域啟用限域同步處理。


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>用以選取要同步至受控網域之群組的指令碼 (Select-GroupsToSync.ps1)
請將下列指令碼儲存至檔案 (```Select-GroupsToSync.ps1```)。 此指令碼會設定 Azure AD Domain Services 以將所選群組同步至受控網域。 屬於所指定群組的所有使用者帳戶都將同步至受控網域。

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>修改群組型限域同步處理
若要修改應將所含使用者同步至受控網域的群組清單，請重新執行 [PowerShell 指令碼](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)，並指定新的群組清單。 請務必一律在此清單中指定 'AAD DC Administrators' 群組。

> [!WARNING]
> **請務必包含 'AAD DC Administrators' 群組。**
>
> 您必須將 'AAD DC Administrators' 群組包含在為限域同步處理設定的群組清單中。 如果未包含此群組，受控網域將無法供使用。
>


## <a name="disable-group-based-scoped-synchronization"></a>停用群組型限域同步處理
請使用下列 PowerShell 指令碼來停用受控網域的群組型限域同步處理：

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>後續步驟
* [了解 Azure AD Domain Services 中的同步處理](synchronization.md)
* [使用 PowerShell 啟用 Azure Active Directory Domain Services](powershell-create-instance.md)
