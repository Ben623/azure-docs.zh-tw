---
title: 在 Azure DevTest Labs 中自動新增實驗室使用者 |Microsoft Docs
description: 本文說明如何使用 Azure Resource Manager 範本、PowerShell 和 CLI，在 Azure DevTest Labs 中自動將使用者新增至實驗室。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718134"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中自動將實驗室使用者新增至實驗室
Azure DevTest Labs 可讓您使用 Azure 入口網站快速建立自助開發/測試環境。 不過，如果您有數個小組和數個 DevTest Labs 實例，自動化建立程式就可以節省時間。 [Azure Resource Manager 範本](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)可讓您以自動化的方式建立實驗室、實驗室 vm、自訂映射、公式及新增使用者。 本文特別著重于將使用者新增至 DevTest Labs 實例。

若要將使用者新增至實驗室，請將使用者新增至實驗室的**DevTest Labs 使用者**角色。 本文說明如何使用下列其中一種方式，自動將使用者新增至實驗室：

- Azure 資源管理員範本
- Azure PowerShell Cmdlet 
- Azure CLI。

## <a name="use-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本
下列範例 Resource Manager 範本會指定要新增至實驗室的**DevTest Labs 使用者**角色的使用者。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

如果您要在建立實驗室的相同範本中指派角色，請記得在角色指派資源與實驗室之間新增相依性。 如需詳細資訊，請參閱[在 Azure Resource Manager 範本中定義](../azure-resource-manager/templates/define-resource-dependency.md)相依性一文。

### <a name="role-assignment-resource-information"></a>角色指派資源資訊
角色指派資源必須指定類型和名稱。

要注意的第一件事是，資源的型別不 `Microsoft.Authorization/roleAssignments`，就像資源群組一樣。  相反地，資源類型會遵循模式 `{provider-namespace}/{resource-type}/providers/roleAssignments`。 在此情況下，資源類型將會 `Microsoft.DevTestLab/labs/providers/roleAssignments`。

角色指派名稱本身必須是全域唯一的。  指派的名稱會使用模式 `{labName}/Microsoft.Authorization/{newGuid}`。 `newGuid` 是範本的參數值。 它可確保角色指派名稱是唯一的。 因為沒有可建立 Guid 的範本函式，所以您需要使用任何 GUID 產生器工具自行產生 GUID。  

在範本中，角色指派的名稱是由 `fullDevTestLabUserRoleName` 變數所定義。 範本中的確切行如下：

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>角色指派資源屬性
角色指派本身會定義三個屬性。 它需要 `roleDefinitionId`、`principalId`和 `scope`。

### <a name="role-definition"></a>角色定義
角色定義 ID 是現有角色定義的字串識別碼。 角色識別碼的格式為 `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`。 

訂用帳戶識別碼是使用 `subscription().subscriptionId` 範本函數取得。  

您需要取得 `DevTest Labs User` 內建角色的角色定義。 若要取得[DevTest Labs 使用者](../role-based-access-control/built-in-roles.md#devtest-labs-user)角色的 GUID，您可以使用 REST API 或[get-azroledefinition 指令程式](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)的[角色指派](/rest/api/authorization/roleassignments)。

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

角色識別碼會定義在 variables 區段中，並命名為 `devTestLabUserRoleId`。 在範本中，角色識別碼會設定為：111111111-0000-0000-11111111111111111。 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>主體識別碼
主體識別碼是您想要新增為實驗室使用者的 Active Directory 使用者、群組或服務主體的物件識別碼。 範本會使用 `ObjectId` 做為參數。

您可以使用[get-azurermaduser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0)、[AzureRMADGroup] 或[new-azurermadserviceprincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell Cmdlet 來取得 ObjectId。 這些 Cmdlet 會傳回具有 ID 屬性的 Active Directory 物件的單一或清單，這是您所需的物件識別碼。 下列範例示範如何取得公司中單一使用者的物件識別碼。

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

您也可以使用 Azure Active Directory 的 PowerShell Cmdlet，其中包括[set-msoluser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0)、 [get-msolgroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)和[new-msolserviceprincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)。

### <a name="scope"></a>影響範圍
範圍指定應該套用角色指派的資源或資源群組。 針對資源，範圍的格式為： `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`。 此範本會使用 `subscription().subscriptionId` 函式來填入 `subscription-id` 部分和 `resourceGroup().name` 範本函式，以填入 `resource-group-name` 部分。 使用這些函式表示您要為其指派角色的實驗室，必須存在於目前的訂用帳戶中，以及部署範本所使用的相同資源群組。 最後一個部分（`resource-name`）是實驗室的名稱。 此值是透過此範例中的樣板參數所接收。 

範本中的角色範圍： 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>部署範本
首先，建立傳遞 Resource Manager 範本中參數值的參數檔案（例如： azuredeploy.parameters.json」. parameters. json）。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

然後，使用[New-azurermresourcegroupdeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell Cmdlet 來部署 Resource Manager 範本。 下列範例命令會將個人、群組或服務主體指派給實驗室的 DevTest Labs 使用者角色。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

請務必注意，群組部署名稱和角色指派 GUID 必須是唯一的。 如果您嘗試使用非唯一的 GUID 來部署資源指派，則會收到 `RoleAssignmentUpdateNotPermitted` 錯誤。

如果您打算使用範本數次，將數個 Active Directory 物件新增至實驗室的 DevTest Labs 使用者角色，請考慮在 PowerShell 命令中使用動態物件。 下列範例會使用[新的-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) Cmdlet，以動態方式指定資源群組部署名稱和角色指派 Guid。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
如簡介中所述，您會建立新的 Azure 角色指派，以將使用者新增至實驗室的**DevTest Labs 使用者**角色。 在 PowerShell 中，您可以使用[new-azurermroleassignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Cmdlet 來執行此動作。 此 Cmdlet 有許多選擇性參數可讓您彈性。 `ObjectId`、`SigninName`或 `ServicePrincipalName` 可以指定為被授與許可權的物件。  

以下是在指定的實驗室中，將使用者新增至 DevTest Labs 使用者角色的範例 Azure PowerShell 命令。

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

若要指定要授與許可權的資源，可以透過 `ResourceName`、`ResourceType`、`ResourceGroup` 或 `scope` 參數的組合來指定。 使用任何組合的參數時，請提供足夠的資訊給 Cmdlet，以唯一識別 Active Directory 物件（使用者、群組或服務主體）、範圍（資源群組或資源）和角色定義。

## <a name="use-azure-command-line-interface-cli"></a>使用 Azure 命令列介面（CLI）
在 Azure CLI 中，將實驗室使用者新增至實驗室的作業是使用 `az role assignment create` 命令來完成。 如需 Azure CLI Cmdlet 的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 來管理 Azure 資源的存取權](../role-based-access-control/role-assignments-cli.md)。

要授與存取權的物件可以由 `objectId`、`signInName``spn` 參數指定。 物件被授與存取權的實驗室可以透過 `scope` url 或 `resource-name`、`resource-type`和 `resource-group` 參數的組合來識別。

下列 Azure CLI 範例示範如何為指定的實驗室將人員新增至 DevTest Labs 使用者角色。  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [使用 Azure CLI 建立及管理具有 DevTest Labs 的虛擬機器](devtest-lab-vmcli.md)
- [使用 Azure PowerShell 建立具有 DevTest Labs 的虛擬機器](devtest-lab-vm-powershell.md)
- [使用命令列工具來啟動和停止 Azure DevTest Labs 虛擬機器](use-command-line-start-stop-virtual-machines.md)

