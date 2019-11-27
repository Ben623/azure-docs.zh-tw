---
title: 使用 RBAC 和 Azure CLI 管理對 Azure 資源的存取 |Microsoft Docs
description: 了解如何使用角色型存取控制 (RBAC) 和 Azure CLI 來管理使用者、群組和應用程式對 Azure 資源的存取。 這包括如何列出存取權、授與存取權以及移除存取權。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 795a97f84bebf6c0e7c1692e82df2f7ce11e0bbd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384089"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>使用 RBAC 與 Azure CLI 管理對 Azure 資源的存取

[角色型存取控制 (RBAC)](overview.md) 是您對 Azure 資源存取進行管理的機制。 本文將描述如何使用 RBAC 和 Azure CLI 來管理使用者、群組和應用程式的存取權。

## <a name="prerequisites"></a>先決條件

若要管理存取權，您需要下列其中一項：

* [Azure Cloud Shell 中的 Bash](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>列出角色

若要列出所有可用的角色定義，請使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

下列範例會列出所有可用角色定義的名稱和描述：

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

下列範例會列出所有內建角色定義：

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>列出角色定義

若要列出角色定義，請使用[az role definition list](/cli/azure/role/definition#az-role-definition-list)：

```azurecli
az role definition list --name <role_name>
```

下列範例會列出「參與者」的角色定義：

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>列出角色的動作

下列範例只會列出*參與者*角色的*動作*和*notActions* ：

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

下列範例只會列出「*虛擬機器參與者*」角色的動作：

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>列出存取權

在 RBAC 中，若要列出存取權，您可以列出角色指派。

### <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出特定使用者的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --assignee <assignee>
```

根據預設，只會顯示範圍為訂用帳戶的直接指派。 若要查看資源或群組範圍內的指派，請使用 `--all` 並使用 `--include-inherited`來查看繼承的指派。

下列範例會列出直接指派給*patlong\@contoso.com*使用者的角色指派：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>列出資源群組範圍的角色指派

若要列出存在於資源群組範圍的角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --resource-group <resource_group>
```

下列範例會列出*醫藥-sales*資源群組的角色指派：

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>列出訂用帳戶範圍的角色指派

若要列出訂用帳戶範圍中的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>列出管理群組範圍的角色指派

若要列出管理群組範圍的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得管理群組識別碼，您可以在 **管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[az 帳戶管理-群組清單](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="get-object-ids"></a>取得物件識別碼

若要列出、新增或移除角色指派，您可能需要指定物件的唯一識別碼。 識別碼的格式為： `11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 入口網站或 Azure CLI 取得識別碼。

### <a name="user"></a>使用者

若要取得 Azure AD 使用者的物件識別碼，您可以使用[az AD user show](/cli/azure/ad/user#az-ad-user-show)。

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>群組

若要取得 Azure AD 群組的物件識別碼，您可以使用[az ad group show](/cli/azure/ad/group#az-ad-group-show)或[az ad group list](/cli/azure/ad/group#az-ad-group-list)。

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>應用程式

若要取得 Azure AD 服務主體（應用程式所使用的身分識別）的物件識別碼，您可以使用[az AD sp list](/cli/azure/ad/sp#az-ad-sp-list)。 針對服務主體，請使用物件識別碼，而**不**是應用程式識別碼。

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="grant-access"></a>授與存取權

在 RBAC 中，若要授與存取權，您可以建立角色指派。

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>為資源群組範圍的使用者建立角色指派

若要將存取權授與資源群組範圍中的使用者，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

下列範例會將「*虛擬機器參與者*」角色指派給*醫藥-sales*資源群組範圍中的*patlong\@contoso.com*使用者：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>使用唯一角色識別碼建立角色指派

有一些時間可能會變更角色名稱，例如：

- 您會使用自己的自訂角色，並決定變更名稱。
- 您在名稱中使用的預覽角色具有 **（預覽）** 。 釋放角色時，會重新命名角色。

> [!IMPORTANT]
> 預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使角色已重新命名，角色識別碼也不會變更。 如果您使用腳本或自動化來建立角色指派，最佳做法是使用唯一角色識別碼，而不是角色名稱。 因此，如果重新命名角色，您的腳本比較有可能會使用。

若要使用唯一角色識別碼（而非角色名稱）來建立角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

下列範例會將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給*醫藥-sales*資源群組範圍中的*patlong\@contoso.com*使用者。 若要取得唯一角色識別碼，您可以使用[az role definition list](/cli/azure/role/definition#az-role-definition-list)或參閱[Azure 資源的內建角色](built-in-roles.md)。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>建立群組的角色指派

若要授與群組的存取權，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 如需如何取得群組物件識別碼的詳細資訊，請參閱[取得物件](#get-object-ids)識別碼。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將*讀取*者角色指派給訂用帳戶範圍中識別碼為22222222-2222-2222-2222-222222222222 的*王 mack」小組*群組。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

下列範例會將「*虛擬機器參與者*」角色指派給名稱為22222222-2222-2222-2222-222222222222 的*王 mack」小組*群組，其位於名為*醫藥*的虛擬網路的資源範圍。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>在資源群組範圍內建立應用程式的角色指派

若要授與應用程式的存取權，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 如需如何取得應用程式物件識別碼的詳細資訊，請參閱[取得物件](#get-object-ids)識別碼。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

下列範例會將「*虛擬機器參與者*」角色指派給*醫藥-sales*資源群組範圍中物件識別碼為44444444-4444-4444-4444-444444444444 的應用程式。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>在訂用帳戶範圍建立使用者的角色指派

若要將存取權授與訂用帳戶範圍的使用者，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

下列範例會將「*讀取*者」角色指派給訂用帳戶範圍中的*annm\@example.com*使用者。

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>為管理群組範圍的使用者建立角色指派

若要將存取權授與管理群組範圍的使用者，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 若要取得管理群組識別碼，您可以在 **管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[az 帳戶管理-群組清單](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

下列範例會將「*帳單讀者*」角色指派給管理群組範圍的*alain\@example.com*使用者。

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>為新的服務主體建立角色指派

如果您建立新的服務主體，並立即嘗試將角色指派給該服務主體，在某些情況下，該角色指派可能會失敗。 例如，如果您使用腳本來建立新的受控識別，然後嘗試將角色指派給該服務主體，則角色指派可能會失敗。 此失敗的原因可能是複寫延遲。 服務主體會建立在一個區域中;不過，角色指派可能會發生在另一個尚未複寫服務主體的區域中。 若要解決這種情況，您應該在建立角色指派時指定主體類型。

若要建立角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)，指定 `--assignee-object-id`的值，然後將 `--assignee-principal-type` 設定為 `ServicePrincipal`。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將「*虛擬機器參與者*」角色指派給*醫藥-sales*資源群組範圍內的*msi 測試*受控識別：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>移除存取

在 RBAC 中，若要移除存取權，您可以使用 [az 角色指派刪除](/cli/azure/role/assignment#az-role-assignment-delete)來移除角色指派:

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

下列範例會從*醫藥-sales*資源群組上的*patlong\@contoso.com*使用者移除「*虛擬機器參與者*」角色指派：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

下列範例會在訂用帳戶範圍中，從識別碼為22222222-2222-2222-2222-222222222222 的*王 Mack」小組*群組移除*讀取*者角色。 如需如何取得群組物件識別碼的詳細資訊，請參閱[取得物件](#get-object-ids)識別碼。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

下列範例會從管理群組範圍的*alain\@example.com*使用者移除「*帳單讀者*」角色。 若要取得管理群組的識別碼，您可以使用[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure CLI 建立適用于 Azure 資源的自訂角色](tutorial-custom-role-cli.md)
- [使用 Azure CLI 管理 Azure 資源和資源群組](../azure-resource-manager/cli-azure-resource-manager.md)
