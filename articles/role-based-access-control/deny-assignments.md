---
title: 了解 Azure 資源的拒絕指派 | Microsoft Docs
description: 了解 Azure 資源之角色型存取控制 (RBAC) 中的拒絕指派。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 2c663b587d2e9ee278fc774c2841899b060ccbcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479365"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>了解 Azure 資源的拒絕指派

與角色指派相同，「拒絕指派」也會基於拒絕存取權的目的來附加一組在特定範圍內拒絕使用者、群組或服務主體的動作。 拒絕指派會封鎖使用者執行指定的 Azure 資源動作，即使角色指派授予他們存取權也一樣。

此文章說明如何定義拒絕指派。

## <a name="how-deny-assignments-are-created"></a>How deny assignments are created

Deny assignments are created and managed by Azure to protect resources. Azure Blueprints and Azure managed apps use deny assignments to protect system-managed resources. Azure Blueprints and Azure managed apps are the only way that deny assignments can be created. You can't directly create your own deny assignments.  如需詳細資訊，請參閱[使用 Azure 藍圖資源鎖定保護新資源](../governance/blueprints/tutorials/protect-new-resources.md)。

> [!NOTE]
> You can't directly create your own deny assignments.

## <a name="compare-role-assignments-and-deny-assignments"></a>Compare role assignments and deny assignments

Deny assignments follow a similar pattern as role assignments, but also have some differences.

| 功能 | 角色指派 | Deny assignment |
| --- | --- | --- |
| 授與存取權 | :heavy_check_mark: |  |
| 拒絕存取 |  | :heavy_check_mark: |
| Can be directly created | :heavy_check_mark: |  |
| Apply at a scope | :heavy_check_mark: | :heavy_check_mark: |
| Exclude principals |  | :heavy_check_mark: |
| Prevent inheritance to child scopes |  | :heavy_check_mark: |
| Apply to [classic subscription administrator](rbac-and-directory-admin-roles.md) assignments |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>拒絕指派屬性

 拒絕指派有下列屬性：

> [!div class="mx-tableFixed"]
> | 屬性 | 必要項 | Type | 描述 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 是 | String | 拒絕指派的顯示名稱。 名稱在指定範圍內必須是唯一的。 |
> | `Description` | 否 | String | 拒絕指派的描述。 |
> | `Permissions.Actions` | 至少一個 Actions 或一個 DataActions | String[] | 一個字串陣列，指定拒絕指派要封鎖存取權的管理作業。 |
> | `Permissions.NotActions` | 否 | String[] | 一個字串陣列，指定要從拒絕指派排除的管理作業。 |
> | `Permissions.DataActions` | 至少一個 Actions 或一個 DataActions | String[] | 一個字串陣列，指定拒絕指派要封鎖存取權的資料作業。 |
> | `Permissions.NotDataActions` | 否 | String[] | 一個字串陣列，指定要從拒絕指派排除的資料作業。 |
> | `Scope` | 否 | String | 一個字串， 指定拒絕指派要套用的範圍。 |
> | `DoNotApplyToChildScopes` | 否 | Boolean | 指定拒絕指派是否要套用到子範圍。 預設值為 false。 |
> | `Principals[i].Id` | 是 | String[] | 要套用拒絕指派的 Azure AD 主體物件識別碼 (使用者、群組、服務主體或受控識別) 陣列。 設定為空 GUID `00000000-0000-0000-0000-000000000000` 以代表所有主體。 |
> | `Principals[i].Type` | 否 | String[] | An array of object types represented by Principals[i].Id. Set to `SystemDefined` to represent all principals. |
> | `ExcludePrincipals[i].Id` | 否 | String[] | 不套用拒絕指派的 Azure AD 主體物件識別碼 (使用者、群組、服務主體或受控識別) 陣列。 |
> | `ExcludePrincipals[i].Type` | 否 | String[] | 由 ExcludePrincipals[i].Id 代表的物件類型陣列。 |
> | `IsSystemProtected` | 否 | Boolean | 指定此拒絕指派是否由 Azure 建立且無法編輯或刪除。 目前，所有拒絕指派都受系統保護。 |

## <a name="the-all-principals-principal"></a>The All Principals principal

To support deny assignments, a system-defined principal named *All Principals* has been introduced. 此主體代表 Azure AD 目錄中的所有使用者、群組、服務主體和受控識別。 若主體識別碼是零值 GUID `00000000-0000-0000-0000-000000000000` 且主體類型是 `SystemDefined`，則主體代表所有主體。 In Azure PowerShell output, All Principals looks like the following:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

All Principals can be combined with `ExcludePrincipals` to deny all principals except some users. All Principals has the following constraints:

- 只能在 `Principals` 中使用，而無法在 `ExcludePrincipals` 中使用。
- `Principals[i].Type` 必須設為 `SystemDefined`。

## <a name="next-steps"></a>後續步驟

* [List deny assignments for Azure resources using the Azure portal](deny-assignments-portal.md)
* [了解 Azure 資源的角色定義](role-definitions.md)
