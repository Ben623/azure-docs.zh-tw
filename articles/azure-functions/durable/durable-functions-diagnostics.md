---
title: 長期函式中的診斷 - Azure
description: 了解如何在 Azure Functions 的「長期函式」延伸模組中實作診斷問題。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7c02d4dfde7869da7985817b06f6de398bbef38d
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734499"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>在 Azure 中診斷 Durable Functions

診斷[長期函式](durable-functions-overview.md)的問題有數個選項。 其中一些選項與一般函式相同，某些則是長期函式特有的。

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 是在 Azure Functions 中診斷和監視的建議方式。 一樣適用於長期函式。 如需如何在您的函式應用程式中利用 Application Insights 的概觀，請參閱[監視 Azure Functions](../functions-monitoring.md)。

Azure Functions 長期延伸模組也會發出「追蹤事件」，可讓您追蹤協調流程的端對端執行。 可以使用 Azure 入口網站中的 [Application Insights Analytics](../../azure-monitor/app/analytics.md) 工具尋找及查詢。

### <a name="tracking-data"></a>追蹤資料

協調流程執行個體的每個生命週期事件會讓追蹤事件寫入到 Application Insights 中的**追蹤**集合。 這個事件包含具有數個欄位的 **customDimensions** 裝載。  欄位名稱前面都會加上 `prop__`。

* **hubName**：您的協調流程執行所在的工作中樞名稱。
* **appName**：函數應用程式的名稱。 當您有多個函式應用程式共用相同的 Application Insights 執行個體時，這非常有用。
* **slotName**：[部署位置](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) \(英文\)，目前的函式應用程式正在其中執行。 當您利用部署位置來設定協調流程版本時，這非常有用。
* **functionName**：協調器或活動函式的名稱。
* **functionType**：函式的類型，例如**協調器**或**活動**。
* **instanceId**：協調流程執行個體的唯一識別碼。
* **state**：執行個體的生命週期執行狀態。 有效值包含：
  * **已排程**：函式已排程執行，但是尚未開始執行。
  * **已啟動**：函式已開始執行，但是尚未等候或已完成。
  * **等候**：協調器已排程一些工作，並且正在等候完成。
  * **正在接聽**：協調器正在接聽外部事件通知。
  * **已完成**：函式已順利完成。
  * **失敗**：函式失敗且發生錯誤。
* **原因**：與追蹤事件相關聯的其他資料。 例如，如果執行個體正在等候外部事件通知，這個欄位會指出它正在等候之事件的名稱。 如果函式失敗，會包含錯誤詳細資料。
* **isReplay**：布林值，指出追蹤事件是否要重新執行。
* **extensionVersion**：持久工作擴充功能的版本。 報告延伸模組中可能的錯誤時，這個資料特別重要。 如果在長時間執行執行個體執行時發生更新，它可能會報告多個版本。
* **sequenceNumber**：事件的執行序號。 與時間戳記結合，有助於依執行時間排序事件。 請注意，如果主機會在執行個體執行中重新啟動，這個數字將重設為零，所以務必一律先依 timestamp，然後依 sequenceNumber 進行排序。

您可以在 `host.json` 檔案的 `logger` (Functions 1.x) 或 `logging` (Functions 2.x) 區段中，設定發出至 Application Insights 之追蹤資料的詳細資訊。

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

根據預設，會發出所有非重新執行的追蹤事件。 藉由將 `Host.Triggers.DurableTask` 設定為 `"Warning"` 或 `"Error"` 可以降低資料量，在此情況下，追蹤事件只會針對例外情況發出。

若要啟用發出詳細的協調流程重新執行事件，在 `durableTask` 底下的 `host.json` 檔案中 `LogReplayEvents` 可以設定為 `true`，如下所示：

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 根據預設，Application Insights 遙測是由 Azure Functions 執行階段取樣，以避免過於頻繁發出資料。 這會在短期內發生太多生命週期事件時，造成追蹤資訊遺失。 [Azure Functions 監視文章](../functions-monitoring.md#configure-sampling)會說明如何設定這個行為。

### <a name="single-instance-query"></a>單一執行個體查詢

下列查詢顯示 [Hello Sequence](durable-functions-sequence.md) 函式協調流程之單一執行個體的歷史追蹤資料。 它是使用 [Application Insights 查詢語言 (AIQL)](https://aka.ms/LogAnalyticsLanguageReference) 寫入的。 它會篩選重新執行，以便僅顯示「邏輯」執行路徑。 您可以藉由排序 `timestamp` 和 `sequenceNumber` 來排序事件，如下列查詢中所示：

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

結果是一份追蹤事件的清單，顯示協調流程的執行路徑，包括依執行時間以遞增順序排序任何活動函式。

![Application Insights 查詢](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>執行個體摘要查詢

下列查詢會顯示在指定時間範圍內執行之所有協調流程執行個體的狀態。

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

結果是執行個體識別碼的清單，以及其目前的執行階段狀態。

![Application Insights 查詢](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>記錄

直接從協調器函式寫入記錄時，請務必記住協調器重新執行行為。 例如，請考慮下列協調器函式：

### <a name="precompiled-c"></a>先行編譯 C#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>C# 指令碼

```csharp
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

產生的記錄資料看起來會像下面這樣：

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> 請記住，當記錄宣告要呼叫 F1、F2 和 F3 時，這些函式只會在第一次遇到時「實際」被呼叫。 會略過重新執行期間發生的後續呼叫，且輸出會重新執行至協調器邏輯。

如果您只想要記錄非重新執行的執行，您可以寫入條件運算式，只有在如果 `IsReplaying` 是 `false` 時記錄。 請考慮上面的範例，但是這次使用重新執行檢查。

#### <a name="precompiled-c"></a>先行編譯 C#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

透過這項變更，記錄輸出如下所示：

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>自訂狀態

自訂協調流程狀態可讓您為協調器函式設定自訂狀態值。 該狀態可透過 HTTP 狀態查詢 API 或 `DurableOrchestrationClient.GetStatusAsync` API 提供。 自訂協調流程狀態能更進一步監視協調器函式。 例如，協調器函式程式碼可以包含 `DurableOrchestrationContext.SetCustomStatus` 呼叫，以更新長期執行作業的進度。 用戶端 (例如網頁或其他外部系統) 則無法定期查詢 HTTP 狀態查詢 API，以取得更豐富的進度資訊。 只用 `DurableOrchestrationContext.SetCustomStatus` 的範例如下所示：

### <a name="precompiled-c"></a>先行編譯 C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

當協調流程執行時，外部用戶端能擷取該自訂狀態：

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

用戶端將取得下列回應：

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> 自訂狀態承載僅限為 16 KB 的 UTF-16 JSON 文字，因為它必須符合 Azure 資料表儲存體資料行的大小。 如果您需要較大的承載，可以使用外部儲存體。

## <a name="debugging"></a>偵錯

Azure Functions 支援直接偵錯函式程式碼，相同支援適用於長期函式，無論是在 Azure 中或在本機執行。 不過，在偵錯時有一些要注意的行為：

* **重新執行**：收到新的輸入時，定期重新執行協調器函式。 這表示單一「邏輯」執行協調器函式會導致叫用相同的中斷點多次，特別是當它先前已在函式程式碼中設定。
* **Await**：每當遇到 `await` 時，它會將控制權讓回給持久的工作架構發送器。 如果這是第一次遇到特定 `await`，相關聯工作「永不」繼續。 因為工作永遠不會繼續，所以「跨越」等候 (在 Visual Studio 中為 F10) 實際上不可能。 跨越只有在重新執行工作時可行。
* **訊息逾時**：Durable Functions 會在內部使用佇列訊息，以驅動協調器函式和活動函式的執行。 在多部 VM 的環境中，中斷至偵錯一段延伸的時間可能會造成另一個 VM 選取訊息，導致重複執行。 這種行為也會結束一般佇列觸發函式，但是務必要在此內容中指出，因為佇列是實作詳細資料。

> [!TIP]
> 設定中斷點時，如果您只想要在非重新執行的執行中斷，您可以設定條件中斷點，只有當 `IsReplaying` 是 `false` 時中斷。

## <a name="storage"></a>儲存體

根據預設，長期函式會將狀態儲存在 Azure 儲存體。 這表示您可以使用工具 (例如 [Microsoft Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)) 來檢查協調流程的狀態。

![Azure 儲存體總管螢幕擷取畫面](./media/durable-functions-diagnostics/storage-explorer.png)

這對於偵錯相當有用，因為您會確實看到協調流程的狀態。 也可以檢查佇列中的訊息來了解哪些工作擱置 (在某些情況下停滯)。

> [!WARNING]
> 可以很方便地查看資料表儲存體中的執行歷程記錄，而且避免此資料表上有任何相依性。 當 Durable Functions 擴充功能進化時，此相依性可能會改變。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何使用長期計時器](durable-functions-timers.md)