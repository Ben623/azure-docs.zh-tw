---
title: 使用 RBAC 和 REST API 來管理 Azure 資源的存取權-Azure |Microsoft Docs
description: 了解如何使用角色型存取控制 (RBAC) 和 REST API 來管理使用者、群組和應用程式對 Azure 資源的存取權。 這包括如何列出存取權、授與存取權以及移除存取權。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996478"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>使用 RBAC 和 REST API 管理對 Azure 資源的存取

[角色型存取控制 (RBAC)](overview.md) 是您對 Azure 資源存取進行管理的機制。 本文將描述如何使用 RBAC 和 REST API 來管理使用者、群組和應用程式的存取權。

## <a name="list-access"></a>列出存取權

在 RBAC 中，若要列出存取權，您可以列出角色指派。 若要列出角色指派，請使用其中一個[角色指派 - 列出](/rest/api/authorization/roleassignments/list) REST API。 若要精簡您的結果，請指定範圍和選擇性篩選條件。

1. 從下列要求著手：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 中，將 *{scope}* 取代為要列出角色指派的範圍。

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    | `subscriptions/{subscriptionId1}` | 訂閱 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    在上述範例中，microsoft web 是參考 App Service 實例的資源提供者。 同樣地，您可以使用任何其他資源提供者並指定範圍。 如需詳細資訊，請參閱[Azure 資源提供者和類型](../azure-resource-manager/resource-manager-supported-services.md)和支援的[Azure Resource Manager 資源提供者作業](resource-provider-operations.md)。  
     
1. 將 *{filter}* 取代為您要針對角色指派清單篩選套用的條件。

    | 篩選器 | 描述 |
    | --- | --- |
    | `$filter=atScope()` | 只會列出指定範圍的角色指派，不包括指派的角色指派。 |
    | `$filter=principalId%20eq%20'{objectId}'` | 列出指定之使用者、群組或服務主體的角色指派。 |
    | `$filter=assignedTo('{objectId}')` | 列出所指定使用者或服務主體的角色指派。 如果使用者是具有角色指派之群組的成員，也會列出該角色指派。 此篩選器可轉移群組，這表示如果使用者是群組的成員，而該群組是具有角色指派之另一個群組的成員，則也會列出該角色指派。 此篩選器只接受使用者或服務主體的物件識別碼。 您無法傳遞群組的物件識別碼。 |

## <a name="grant-access"></a>授與存取權

在 RBAC 中，若要授與存取權，您可以建立角色指派。 若要建立角色指派，請使用 [角色指派 - 建立](/rest/api/authorization/roleassignments/create) REST API，並指定安全性主體、角色定義和範圍。 若要呼叫這個 API，您必須具有 `Microsoft.Authorization/roleAssignments/write` 作業的存取權。 在內建角色中，只有[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)會獲得這項作業的存取權。

1. 使用[角色定義 - 列出](/rest/api/authorization/roledefinitions/list) REST API 或參閱[內建角色](built-in-roles.md)，以取得您要指派角色定義的識別碼。

1. 使用 GUID 工具來產生將用於角色指派識別碼的唯一識別碼。 此識別碼的格式：`00000000-0000-0000-0000-000000000000`

1. 從下列要求和本文著手：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. 在 URI 中，將 *{scope}* 取代為角色指派的範圍。

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    | `subscriptions/{subscriptionId1}` | 訂閱 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. 將 *{roleAssignmentName}* 取代為角色指派的 GUID 識別碼。

1. 在要求主體中，將 *{scope}* 取代為角色指派的範圍。

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    | `subscriptions/{subscriptionId1}` | 訂閱 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. 將 *{roleDefinitionId}* 取代為角色定義識別碼。

1. 將 *{principalId}* 取代為使用者、群組或服務主體 (將獲得角色指派) 的物件識別碼。

## <a name="remove-access"></a>移除存取權

在 RBAC 中，若要移除存取權，您可以移除角色指派。 若要移除角色指派，請使用[角色指派 - 刪除](/rest/api/authorization/roleassignments/delete) REST API。 若要呼叫這個 API，您必須具有 `Microsoft.Authorization/roleAssignments/delete` 作業的存取權。 在內建角色中，只有[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)會獲得這項作業的存取權。

1. 取得角色指派識別碼 (GUID)。 您第一次建立角色指派時會傳回這個識別碼，或者可藉由列出角色指派來取得它。

1. 從下列要求著手：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. 在 URI 中，將 *{scope}* 取代為要移除角色指派的範圍。

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    | `subscriptions/{subscriptionId1}` | 訂閱 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 資源群組 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. 將 *{roleAssignmentName}* 取代為角色指派的 GUID 識別碼。

## <a name="next-steps"></a>後續步驟

- [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API 參考](/rest/api/azure/)
- [使用 REST API 建立 Azure 資源的自訂角色](custom-roles-rest.md)
