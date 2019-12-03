---
title: 使用 Azure RBAC 和 REST API 新增或移除角色指派
description: 瞭解如何使用 Azure 角色型存取控制（RBAC）和 REST API，為使用者、群組、服務主體或受控識別授與 Azure 資源的存取權。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1ba0c2bd81f32c0aec242dbfb32b2d7f4064ddbe
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707837"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>使用 Azure RBAC 和 REST API 新增或移除角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文說明如何使用 REST API 指派角色。

## <a name="prerequisites"></a>必要條件

若要新增或移除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>新增角色指派

在 RBAC 中，若要授與存取權，您可以新增角色指派。 若要新增角色指派，請使用[角色指派-建立](/rest/api/authorization/roleassignments/create)REST API 並指定安全性主體、角色定義和範圍。 若要呼叫這個 API，您必須具有 `Microsoft.Authorization/roleAssignments/write` 作業的存取權。 在內建角色中，只有[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)會獲得這項作業的存取權。

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

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | 資源 |

1. 將 *{roleAssignmentName}* 取代為角色指派的 GUID 識別碼。

1. 在要求主體中，將 *{scope}* 取代為角色指派的範圍。

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | 資源 |

1. 將 *{roleDefinitionId}* 取代為角色定義識別碼。

1. 將 *{principalId}* 取代為使用者、群組或服務主體 (將獲得角色指派) 的物件識別碼。

## <a name="remove-a-role-assignment"></a>移除角色指派

在 RBAC 中，若要移除存取權，您可以移除角色指派。 若要移除角色指派，請使用[角色指派 - 刪除](/rest/api/authorization/roleassignments/delete) REST API。 若要呼叫這個 API，您必須具有 `Microsoft.Authorization/roleAssignments/delete` 作業的存取權。 在內建角色中，只有[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)會獲得這項作業的存取權。

1. 取得角色指派識別碼 (GUID)。 您第一次建立角色指派時會傳回這個識別碼，或者可藉由列出角色指派來取得它。

1. 從下列要求著手：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. 在 URI 中，將 *{scope}* 取代為要移除角色指派的範圍。

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理群組 |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | 資源 |

1. 將 *{roleAssignmentName}* 取代為角色指派的 GUID 識別碼。

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 REST API 列出角色指派](role-assignments-list-rest.md)
- [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API 參考](/rest/api/azure/)
- [使用 REST API 建立 Azure 資源的自訂角色](custom-roles-rest.md)
