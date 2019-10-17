---
title: 了解查詢語言
description: 描述 Resource Graph 資料表和可用的 Kusto 資料類型、運算子和函數，Azure Resource Graph。
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 6189920cb03a6cf388f0b5d232c6ce97ae4f3f82
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389772"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查詢語言

Azure Resource Graph 查詢語言支援多個運算子與函式。 每個工作並根據[Kusto 查詢語言（KQL）](/azure/kusto/query/index)進行操作。 若要瞭解 Resource Graph 所使用的查詢語言，請從[KQL 的教學](/azure/kusto/query/tutorial)課程開始。

本文涵蓋 Resource Graph 支援的語言元件：

- [Resource Graph 資料表](#resource-graph-tables)
- [支援的 KQL 語言元素](#supported-kql-language-elements)
- [Escape 字元](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph 資料表

Resource Graph 會針對它所儲存的資料，提供有關 Resource Manager 資源類型及其屬性的多個資料表。 這些資料表可以與 `join` 或 @no__t 1 運算子搭配使用，以取得相關資源類型的屬性。 以下是 Resource Graph 中可用的資料表清單：

|Resource Graph 資料表 |描述 |
|---|---|
|資源 |未在查詢中定義的預設資料表。 大部分的 Resource Manager 資源類型和屬性都在這裡。 |
|ResourceContainers |包含訂用帳戶（`Microsoft.Resources/subscriptions`）和資源群組（`Microsoft.Resources/subscriptions/resourcegroups`）資源類型和資料。 |
|AlertsManagementResources |包含與 `Microsoft.AlertsManagement`_相關_的資源。 |
|SecurityResources |包含與 `Microsoft.Security`_相關_的資源。 |

> [!NOTE]
> [_資源_] 是預設資料表。 查詢_Resources_資料表時，除非使用 `join` 或 `union`，否則不需要提供資料表名稱。 不過，建議的做法是在查詢中一律包含初始資料表。

使用入口網站中的 Resource Graph Explorer，探索每個資料表中可用的資源類型。 或者，使用 `<tableName> | distinct type` 之類的查詢來取得給定的 Resource Graph 資料表支援的資源類型清單，而該清單存在於您的環境中。

下列查詢會顯示簡單的 `join`。 查詢結果會將這些資料行結合在一起，而聯結資料表（在此範例中為_ResourceContainers_ ）中任何重複的資料行名稱會附加**1**。 由於_ResourceContainers_資料表具有訂閱和資源群組的類型，因此可能會使用任何一種類型來聯結_資源_資料表中的資源。

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

下列查詢會顯示更複雜的 `join` 用法。 查詢會將聯結的資料表限制為訂用帳戶資源，而 `project` 則只包含原始欄位_subscriptionId_ ，而_名稱_欄位則會重新命名為 _[web.config]_ 。 欄位重新命名可避免 `join` 將其新增為_name1_ ，因為欄位已經存在_資源_中。 原始資料表會使用 `where` 進行篩選，而下列 `project` 則包含兩個數據表的資料行。 查詢結果是顯示類型的單一金鑰保存庫、金鑰保存庫的名稱，以及它所在的訂用帳戶名稱。

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

> [!NOTE]
> 當使用 `project` 來限制 @no__t 0 的結果時，`join` 用來建立兩個數據表關聯的屬性（在上述範例中為_subscriptionId_ ）必須包含在 `project` 中。

## <a name="supported-kql-language-elements"></a>支援的 KQL 語言元素

Resource Graph 支援所有的 KQL[資料類型](/azure/kusto/query/scalar-data-types/)、純量[函數](/azure/kusto/query/scalarfunctions)、純量[運算子](/azure/kusto/query/binoperators)和[彙總函式](/azure/kusto/query/any-aggfunction)。 Resource Graph 支援特定的[表格式運算子](/azure/kusto/query/queries)，其中有些有不同的行為。

### <a name="supported-tabulartop-level-operators"></a>支援的表格式/最上層運算子

以下是 Resource Graph 與特定範例支援的 KQL 表格式運算子清單：

|KQL |Resource Graph 範例查詢 |注意 |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[金鑰保存庫計數](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[顯示特定別名的相異值](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[依作業系統類型計算的虛擬機器計數](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[金鑰保存庫與訂用帳戶名稱](../samples/advanced.md#join) |支援的聯結類別： [innerunique](/azure/kusto/query/joinoperator#default-join-flavor)、 [inner](/azure/kusto/query/joinoperator#inner-join)、 [leftouter](/azure/kusto/query/joinoperator#left-outer-join)。 單一查詢中的限制為 3 `join`。 不允許自訂聯結策略（例如廣播聯結）。 可以在單一資料表內，或在_Resources_和_ResourceContainers_資料表之間使用。 |
|[limit](/azure/kusto/query/limitoperator) |[列出所有公用 IP 位址](../samples/starter.md#list-publicip) |@No__t 的同義字-0 |
|[mv-展開](/azure/kusto/query/mvexpandoperator) |[列出具有特定寫入位置的 Cosmos DB](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_最大值400 |
|[即可](/azure/kusto/query/orderoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) |@No__t 的同義字-0 |
|[project](/azure/kusto/query/projectoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[從結果中移除資料行](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[列出依名稱排序的資源](../samples/starter.md#list-resources) |@No__t 的同義字-0 |
|[summarize](/azure/kusto/query/summarizeoperator) |[計算的 Azure 資源計數](../samples/starter.md#count-resources) |僅限簡化的第一頁 |
|[take](/azure/kusto/query/takeoperator) |[列出所有公用 IP 位址](../samples/starter.md#list-publicip) |@No__t 的同義字-0 |
|[top](/azure/kusto/query/topoperator) |[依名稱顯示前五個虛擬機器及其作業系統類型](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[將兩個查詢的結果合併成單一結果](../samples/advanced.md#unionresults) |允許單一資料表： _T_ `| union` \[ @ no__t-3 `inner` @ no__t-5 @ no__t-6 @ no__t-7 \[ @ no__t-9_ColumnName_1_資料表_。 單一查詢中的限制為 3 `union` 個腿。 不允許 @no__t 的模糊解析度-0 的支線資料表。 可以在單一資料表內，或在_Resources_和_ResourceContainers_資料表之間使用。 |
|[where](/azure/kusto/query/whereoperator) |[顯示包含儲存體的資源](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Escape 字元

某些屬性名稱（例如包含 `.` 或 `$`）必須在查詢中包裝或換行，否則屬性名稱會不正確地轉譯，而且不會提供預期的結果。

- `.`-將屬性名稱換行，如下所示： `['propertyname.withaperiod']`
  
  包裝屬性 odata 的範例查詢 _。類型_：

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-將屬性名稱中的字元轉義。 使用的逸出字元取決於從執行的 shell Resource Graph。

  - **bash** -  @ no__t-2

    在 bash 中 _@no__t 1type_屬性的範例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -不要將 `$` 個字元。

  - **PowerShell** - ``` ` ```

    在 PowerShell 中將屬性 _@no__t_轉義的範例查詢：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式
- 了解[探索資源](explore-resources.md)