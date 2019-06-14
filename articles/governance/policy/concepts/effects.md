---
title: 了解效果的運作方式
description: 「Azure 原則」定義有各種效果，可決定合規性的管理和回報方式。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 6ad6f9414df17f9edff7565752ef3845e0d3c88e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116193"
---
# <a name="understand-azure-policy-effects"></a>了解 Azure 原則效果

「Azure 原則」中的每個原則定義都有一個效果。 該效果決定了當原則規則評估為相符時會發生的情況。 這些效果在針對新資源、已更新的資源或現有的資源時，各有不同的行為表現。

原則定義中目前支援六種效果：

- Append
- 稽核
- AuditIfNotExists
- 拒絕
- DeployIfNotExists
- 已停用

## <a name="order-of-evaluation"></a>評估順序

若要建立或更新資源透過 Azure Resource Manager 要求會先評估 Azure 原則。 Azure 原則會建立一份所有套用到資源，然後再評估資源的每個定義的指派。 Azure 原則處理數個效果，再交給適當的資源提供者的要求。 當資源不符合設計的管理控制，Azure 原則，如此可避免不必要的處理，由資源提供者。

- 首先會檢查 **Disabled**，以決定是否應評估原則規則。
- 接著評估的是 **Append**。 由於 Append 可以改變要求，因此 Append 所進行的變更可能會導致無法觸發 Audit 或 Deny 效果。
- 接著評估的是 **Deny**。 在 Audit 前先評估 Deny 可防止重複記錄不想要的資源。
- 接著會先評估 **Audit**，然後才將要求傳遞給「資源提供者」。

在「資源提供者」傳回成功碼之後，便會評估 **AuditIfNotExists** 和 **DeployIfNotExists**，以判斷是否需要進行後額外的合規性記錄或動作。

## <a name="disabled"></a>已停用

針對測試情況，或當原則定義已將效果參數化時，此效果相當有用。 這個彈性讓您得以停用單一指派，而不是停用該原則的所有指派。

## <a name="append"></a>Append

Append 可用來在建立或更新所要求的資源時，為資源新增額外的欄位。 其中一個常見的範例就是在資源上新增標籤 (例如 costCenter)，或是為儲存體資源指定允許的 IP。

### <a name="append-evaluation"></a>Append 評估

在建立或更新資源期間，會先由 Append 進行評估，然後才由「資源提供者」處理要求。 Append 會在原則規則的 **if** 條件相符時，為資源新增欄位。 如果 Append 效果會以不同的值覆寫原始要求中的值，則它會充當 Deny 效果而拒絕該要求。 若要將新值附加至現有陣列中，請使用 **[\*]** 版本的別名。

當使用 Append 效果的原則定義在評估週期中執行時，並不會對已經存在的資源進行變更。 取而代之的是，會將符合 **if** 條件的所有資源標示為不符合規範。

### <a name="append-properties"></a>Append 屬性

Append 效果只有一個 **details** 陣列且為必要。 由於 **details** 是陣列，因此它可以接受單一的 **field/value** 配對，也可以接受多個配對。 如需了解可接受的欄位清單，請參考[定義結構](definition-structure.md#fields)。

### <a name="append-examples"></a>Append 範例

範例 1：用以附加一個標籤的單一 **field/value** 配對。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

範例 2：用以附加一組標籤的兩個 **field/value** 配對。

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

範例 3：單一**欄位/值**配對使用非 **[\*]** [別名](definition-structure.md#aliases)陣列**值**儲存體帳戶上設定 IP 規則。 當非 **[\*]** 別名是陣列時，效果會將 **value** 當做整個陣列來附加。 如果陣列已經存在，就會因為衝突而發生拒絕事件。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

範例 4︰使用 **[\*]** [別名](definition-structure.md#aliases)搭配 **value** 陣列以設定儲存體帳戶相關 IP 規則的單一 **field/value** 配對。 藉由使用 **[\*]** 別名，效果會將 **value** 附加至可能預先存在的陣列。 如果陣列不存在，則會加以建立。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="deny"></a>拒絕

Deny 可用來透過原則定義防止不符合所定義標準的資源要求，並讓該要求失敗。

### <a name="deny-evaluation"></a>Deny 評估

建立或更新相符的資源時，Deny 可在要求被傳送給「資源提供者」之前先行阻止。 要求會以 `403 (Forbidden)` 的形式傳回。 在入口網站中，可將「禁止」視為原則指派所阻止的部署狀態。

評估現有資源時，符合 Deny 原則定義的資源會標示為不符合規範。

### <a name="deny-properties"></a>Deny 屬性

Deny 效果沒有任何額外的屬性可供在原則定義的 **then** 條件中使用。

### <a name="deny-example"></a>Deny 範例

範例：使用 Deny 效果。

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>稽核

Audit 效果可用來在評估到不符合規範的資源時，在活動記錄中建立警告事件，但並不會停止該項要求。

### <a name="audit-evaluation"></a>Audit 評估

稽核是最後一個期間建立或更新資源的檢查 Azure 原則的效果。 Azure 原則接著會傳送到資源提供者的資源。 Audit 對資源要求和評估週期的運作方式相同。 Azure 原則新增`Microsoft.Authorization/policies/audit/action`至活動記錄的作業，並將標示為不相容資源。

### <a name="audit-properties"></a>Audit 屬性

Audit 效果沒有任何額外的屬性可供在原則定義的 **then** 條件中使用。

### <a name="audit-example"></a>Audit 範例

範例：使用 Audit 效果。

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists 可讓您稽核符合下列條件的資源：符合 **if** 條件，但沒有 **then** 條件中 **details** 所指定的元件。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 評估

AuditIfNotExists 的執行順序是在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行稽核。 Azure 原則新增`Microsoft.Authorization/policies/audit/action`至活動的作業記錄與稽核效果的相同的方式。 當觸發時，滿足 **if** 條件的資源會是標示為不符合規範的資源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 屬性

AuditIfNotExists 效果的 **details** 屬性含有定義所要比對相關資源的所有子屬性。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 如果**details.type**是資源類型底下**如果**條件的資源，原則會查詢以取得這個資源**型別**範圍內的已評估的資源。 否則為原則評估的資源相同資源群組內的查詢。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當條件值**if.field.type**並**then.details.type**相符，然後**名稱**變成_必要_，而且必須是`[field('name')]`. 不過，[稽核](#audit)效果應該改為考慮。
- **ResourceGroupName** (選擇性)
  - 允許比對來自不同資源群組的相關資源。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 預設值為 **if** 條件資源的資源群組。
- **ExistenceScope** (選擇性)
  - 允許的值為 _Subscription_ 和 _ResourceGroup_。
  - 設定要從中擷取所要比對之相關資源的位置範圍。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 針對 _ResourceGroup_，將會限制為 **if** 條件資源的資源群組，或 **ResourceGroupName** 中所指定的資源群組。
  - 針對 _Subscription_，則會查詢相關資源的整個訂用帳戶。
  - 預設值為 _ResourceGroup_。
- **ExistenceCondition** (選擇性)
  - 如果未指定，則 **type** 的任何相關資源都可滿足此效果，而不會觸發稽核。
  - 使用與 **if** 條件的原則規則相同的語言，但會個別針對每個相關資源進行評估。
  - 如果有任何相符的相關資源評估為 true，便可滿足此效果，而不會觸發稽核。
  - 可以使用 [field()] 來檢查是否與 **if** 條件中的值相等。
  - 例如，可用來驗證父資源 (在 **if** 條件中) 是否與相符的相關資源位於相同的資源位置。

### <a name="auditifnotexists-example"></a>AuditIfNotExists 範例

範例：評估「虛擬機器」以判斷「反惡意程式碼軟體」延伸模組是否存在，然後在遺漏該軟體時進行稽核。

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

DeployIfNotExists 與 AuditIfNotExists 類似，也會在符合條件時執行範本部署。

> [!NOTE]
> 使用 **deployIfNotExists** 時，支援[巢狀範本](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template)，但目前不支援[連結的範本](../../../azure-resource-manager/resource-group-linked-templates.md)。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 評估

DeployIfNotExists 的執行順序是在「資源提供者」已處理建立或更新資源要求，並且已傳回成功狀態碼之後。 如果沒有任何相關資源，或 **ExistenceCondition** 所定義的資源未評估為 true，就會進行範本部署。

在評估週期期間，會將含有 DeployIfNotExists 效果且與資源相符的原則定義標示為不符合規範，但不會對該資源採取任何動作。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 屬性

DeployIfNotExists 效果的 **details** 屬性含有定義所要比對相關資源及所要執行範本部署的所有子屬性。

- **Type** [必要]
  - 指定要比對之相關資源的類型。
  - 從嘗試在 **if** 條件資源下擷取資源開始著手，然後在與 **if** 條件資源相同的資源群組內進行查詢。
- **Name** (選擇性)
  - 指定要比對的資源確切名稱，然後使原則擷取一個特定資源，而不是所指定類型的所有資源。
  - 當條件值**if.field.type**並**then.details.type**相符，然後**名稱**變成_必要_，而且必須是`[field('name')]`.
- **ResourceGroupName** (選擇性)
  - 允許比對來自不同資源群組的相關資源。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 預設值為 **if** 條件資源的資源群組。
  - 如果執行範本部署，會在此值的資源群組中部署。
- **ExistenceScope** (選擇性)
  - 允許的值為 _Subscription_ 和 _ResourceGroup_。
  - 設定要從中擷取所要比對之相關資源的位置範圍。
  - 如果 **type** 是一個會在 **if** 條件資源下的資源，則不適用。
  - 針對 _ResourceGroup_，將會限制為 **if** 條件資源的資源群組，或 **ResourceGroupName** 中所指定的資源群組。
  - 針對 _Subscription_，則會查詢相關資源的整個訂用帳戶。
  - 預設值為 _ResourceGroup_。
- **ExistenceCondition** (選擇性)
  - 如果未指定，則 **type** 的任何相關資源都可滿足此效果，而不會觸發部署。
  - 使用與 **if** 條件的原則規則相同的語言，但會個別針對每個相關資源進行評估。
  - 如果有任何相符的相關資源評估為 true，便可滿足此效果，而不會觸發部署。
  - 可以使用 [field()] 來檢查是否與 **if** 條件中的值相等。
  - 例如，可用來驗證父資源 (在 **if** 條件中) 是否與相符的相關資源位於相同的資源位置。
- **roleDefinitionIds** [必要]
  - 此屬性必須包含與訂用帳戶可存取之角色型存取控制角色識別碼相符的字串陣列。 如需詳細資訊，請參閱[補救 - 設定原則定義](../how-to/remediate-resources.md#configure-policy-definition)。
- **DeploymentScope** (選擇性)
  - 允許的值為 _Subscription_ 和 _ResourceGroup_。
  - 設定要觸發的部署類型。 _Subscription_ 表示[在訂用帳戶層級進行部署](../../../azure-resource-manager/deploy-to-subscription.md)、_ResourceGroup_ 表示部署至某個資源群組。
  - 使用訂用帳戶層級部署時，必須在 _Deployment_ 中指定 _location_ 屬性。
  - 預設值為 _ResourceGroup_。
- **Deployment** [必要]
  - 此屬性應該包含完整範本部署，因為它將傳遞給 `Microsoft.Resources/deployments` PUT API。 如需詳細資訊，請參閱[部署 REST API](/rest/api/resources/deployments)。

  > [!NOTE]
  > **Deployment** 屬性內的所有函式都會作為範本 (而非原則) 的元件來評估。 **parameters** 屬性例外，此屬性會將來自原則的值傳遞給範本。 此區段中 template 參數名稱底下的 **value** 會用來執行這項值傳遞 (請參閱 DeployIfNotExists 範例中的 _fullDbName_)。

### <a name="deployifnotexists-example"></a>DeployIfNotExists 範例

範例：評估 SQL Server 資料庫，以判斷是否已啟用 transparentDataEncryption。 如果未啟用，則會執行部署來進行啟用。

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>分層原則

一個資源可能會受到數個指派影響。 這些指派可能屬於相同範圍，也可能屬於不同範圍。 這些指派中的每項指派也可能定義了不同的效果。 針對每個原則的條件和效果，都會以獨立方式進行評估。 例如:

- 原則 1
  - 將資源位置限制為 'westus'
  - 指派給訂用帳戶 A
  - Deny 效果
- 原則 2
  - 將資源位置限制為 'eastus'
  - 指派給訂用帳戶 A 中的資源群組 B
  - Audit 效果
  
此設定會產生下列結果：

- 任何已經在資源群組 B 且在 'eastus' 中的資源都符合原則 2 的規範，但不符合原則 1 的規範
- 任何已經在資源群組 B 但不在 'eastus' 中的資源都不符合原則 2 的規範，且如果不在 'westus' 中，則也不符合原則 1 的規範
- 任何在訂用帳戶 A 但不在 'westus' 中的新資源都會被原則 1 拒絕
- 任何在訂用帳戶 A 和資源群組 B 且在 'westus' 中的新資源都會建立，但不符合原則 2 的規範

如果原則 1 和原則 2 都採用 Deny 效果，則情況會變成：

- 任何已經在資源群組 B 但不在 'eastus' 中的資源都不符合原則 2 的規範
- 任何已經在資源群組 B 但不在 'westus' 中的資源都不符合原則 1 的規範
- 任何在訂用帳戶 A 但不在 'westus' 中的新資源都會被原則 1 拒絕
- 任何在訂用帳戶 A 之資源群組 B 中的新資源都會被拒絕

針對每個指派都會以獨立方式進行評估。 因此，不可能讓資源從範圍差異的間隙中逃脫。 分層原則或原則重疊的淨結果會被視為**累計限制最嚴格的**結果。 舉例來說，如果原則 1 和原則 2 都具有 Deny 效果，則資源會遭到重疊和衝突原則封鎖。 如果您仍然需要在目標範圍中建立該資源，請檢閱每項指派的相關排除項目，以驗證是由正確的原則影響正確的範圍。

## <a name="next-steps"></a>後續步驟

- 檢閱範例[「 Azure 原則範例](../samples/index.md)。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/getting-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。