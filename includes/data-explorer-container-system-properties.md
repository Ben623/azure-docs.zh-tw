---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129265"
---
### <a name="event-system-properties-mapping"></a>事件系統屬性對應

> [!Note]
> * 單一記錄事件支援系統屬性。
> * 若為 `csv` 對應，則會在記錄的開頭加入屬性。 針對 `json` 對應，會根據下拉式清單中顯示的名稱加入屬性。

如果您在資料表的**資料來源**區段中選取了 [**事件系統屬性**]，則必須在資料表架構和對應中加入下列屬性。

**資料表架構範例**

如果您的資料包含三個數據行（`Timespan`、`Metric`和 `Value`），而您包含的屬性是 `x-opt-enqueued-time` 和 `x-opt-offset`，請使用下列命令來建立或改變數據表架構：

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**CSV 對應範例**

執行下列命令，將資料新增至記錄的開頭。 請注意序數值。

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**JSON 對應範例**

使用 [**資料連線**] 分頁**事件系統屬性**清單中所顯示的系統屬性名稱來加入資料。 執行以下命令：

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
