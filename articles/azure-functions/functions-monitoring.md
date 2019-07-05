---
title: 監視 Azure Functions
description: 了解如何使用 Azure Functions 使用 Azure Application Insights 來監視函式執行。
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure functions, 函式, 事件處理, webhook, 動態計算, 無伺服器架構
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 581b7cc09089b5f48938bc9677eca6b9dc3731d3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442299"
---
# <a name="monitor-azure-functions"></a>監視 Azure Functions

[Azure Functions](functions-overview.md)提供與內建的整合[Azure Application Insights](../azure-monitor/app/app-insights-overview.md)來監視函式。 這篇文章會示範如何設定 Azure Functions，以將系統產生記錄檔傳送至 Application Insights。

我們建議使用 Application Insights，因為它會收集記錄檔、 效能和錯誤資料。 它會自動偵測效能異常狀況，並包含功能強大的分析工具可協助您診斷問題，並了解如何使用您的函式。 它是設計來協助您持續改善效能和可用性。 您甚至可以在區域函式應用程式專案的開發期間使用 Application Insights。 如需詳細資訊，請參閱 <<c0> [ 什麼是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。

因為所需的 Application Insights 檢測內建在 Azure Functions，您只需要是有效的檢測金鑰，您的函式應用程式連線到 Application Insights 資源。

## <a name="application-insights-pricing-and-limits"></a>Application Insights 定價和限制

您可以免費嘗試 Azure Application Insights 與 Function Apps 整合。 沒有每日限制的免費可處理多少資料。 您可能會達到此限制，在測試期間。 當您趨近每日限制時，Azure 會提供入口網站及電子郵件通知。 如果您錯過這些警示且達到限制時，新的記錄檔不會出現在 Application Insights 查詢中。 要注意的限制，以避免不必要的疑難排解時間。 如需詳細資訊，請參閱[管理 Application Insights 中的價格和資料磁碟區](../azure-monitor/app/pricing.md)。

## <a name="enable-application-insights-integration"></a>啟用 Application Insights 整合

針對將資料傳送至 Application Insights 的函式應用程式，它需要知道 Application Insights 資源的檢測金鑰。 金鑰必須在名為 **APPINSIGHTS_INSTRUMENTATIONKEY** 的應用程式設定中。

### <a name="new-function-app-in-the-portal"></a>在入口網站中的新函式應用程式

當您[在 Azure 入口網站中建立函式應用程式](functions-create-first-azure-function.md)，預設會啟用 Application Insights 整合。 Application Insights 資源有同名的函式應用程式，它會建立在相同的區域或最接近的區域中。

若要檢閱所建立的 Application Insights 資源，請選取它來依序展開**Application Insights**視窗。 您可以變更**新的資源名稱**，或選擇不同**位置**中[Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)您想要用來儲存您的資料。

![建立函式應用程式時啟用 Application Insights](media/functions-monitoring/enable-ai-new-function-app.png)

當您選擇**Create**，Application Insights 資源會建立函式應用程式，其具有`APPINSIGHTS_INSTRUMENTATIONKEY`應用程式設定中設定。 所有項目已準備就緒。

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>新增至現有的函式應用程式 

當您建立函式應用程式，使用[Azure CLI](functions-create-first-azure-function-azure-cli.md)， [Visual Studio](functions-create-your-first-function-visual-studio.md)，或[Visual Studio Code](functions-create-first-function-vs-code.md)，您必須建立 Application Insights 資源。 您可以再新增檢測金鑰從該資源做為應用程式設定函數應用程式中。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

早期版本的函式使用內建的監視，但不建議這麼做。 當啟用這類函式應用程式的 Application Insights 整合，您必須也[停用內建記錄](#disable-built-in-logging)。  

## <a name="view-telemetry-in-monitor-tab"></a>在監視索引標籤中檢視遙測

具有[啟用 Application Insights 整合](#enable-application-insights-integration)，您可以檢視遙測資料**監視器** 索引標籤。

1. 在函式應用程式頁面中，選取具有至少執行一次之後設定 Application Insights 的函式。 然後選取**監視器** 索引標籤。

   ![選取監視索引標籤](media/functions-monitoring/monitor-tab.png)

1. 選取 **重新整理**定期直到函式引動過程清單隨即出現。

   可能需要五分鐘的時間的遙測用戶端批次資料傳輸到伺服器時出現的清單。 (延遲不會套用到[即時計量 Stream](../azure-monitor/app/live-stream.md)。 當您載入頁面時，該服務會連線到函式主機，讓記錄直接串流處理至頁面。)

   ![引動過程清單](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. 若要查看特定函式引動過程的記錄，請選取該引動過程的 [日期]  資料行連結。

   ![引動過程詳細資料連結](media/functions-monitoring/invocation-details-link-ai.png)

   該引動過程的記錄輸出會顯示在新頁面中。

   ![引動過程詳細資料](media/functions-monitoring/invocation-details-ai.png)

您可以看到這兩個頁面都有**Application Insights 中執行**連結至 Application Insights Analytics 查詢來擷取的資料。

![在 Application Insights 中執行](media/functions-monitoring/run-in-ai.png)

下列查詢會顯示。 您可以看到，引動過程清單是有限的最後一個 30 天。 此清單會顯示不超過 20 個資料列 (`where timestamp > ago(30d) | take 20`)。 過去 30 天內，而且不限制是引動過程詳細資料清單。

![Application Insights 分析引動過程清單](media/functions-monitoring/ai-analytics-invocation-list.png)

如需詳細資訊，請參閱本文稍後的[查詢遙測資料](#query-telemetry-data)。

## <a name="view-telemetry-in-application-insights"></a>在 Application Insights 中檢視遙測

若要從 Azure 入口網站中的函式應用程式中開啟 Application Insights，請移至函數應用程式**概觀**頁面。 底下**設定功能**，選取**Application Insights**。

![從函式應用程式 [概觀] 頁面中開啟 Application Insights](media/functions-monitoring/ai-link.png)

如需如何使用 Application Insights 的相關資訊，請參閱 [Application Insights 文件](https://docs.microsoft.com/azure/application-insights/)。 本節示範一些如何在 Application Insights 中檢視資料的範例。 如果您已經熟悉 Application Insights，您可以直接移至[如何設定及自訂的遙測資料的相關章節](#configure-categories-and-log-levels)。

![Application Insights 概觀 索引標籤](media/functions-monitoring/metrics-explorer.png)

評估行為、 效能和您的函式中的錯誤時，Application Insights 的下列區域可以是很有幫助：

| Tab 鍵 | 描述 |
| ---- | ----------- |
| **[失敗](../azure-monitor/app/asp-net-exceptions.md)** |  建立圖表和函式失敗和伺服器例外狀況為基礎的警示。 **作業名稱**是函式名稱。 除非您實作自訂相依性遙測，不會顯示在 相依性失敗。 |
| **[效能](../azure-monitor/app/performance-counters.md)** | 分析效能問題。 |
| **伺服器** | 檢視資源使用率和每一部伺服器的輸送量。 如果要對函式拖累基礎資源的案例進行偵錯，此資料非常有用。 伺服器會作為「雲端角色執行個體」  來參考。 |
| **[計量](../azure-monitor/app/metrics-explorer.md)** | 建立圖表和計量為基礎的警示。 度量包括函式引動過程、 執行時間和成功率的數目。 |
| **[即時計量串流](../azure-monitor/app/live-stream.md)** | 即時建立時，請檢視度量資料。 |

## <a name="query-telemetry-data"></a>查詢遙測資料

[Application Insights Analytics](../azure-monitor/app/analytics.md)可讓您存取的資料庫中資料表形式的所有遙測資料。 分析會提供用於擷取、操作和視覺化資料的查詢語言。

![選取 [分析]](media/functions-monitoring/select-analytics.png)

![分析範例](media/functions-monitoring/analytics-traces.png)

以下的查詢範例會顯示過去 30 分鐘內每個背景工作角色的要求分佈狀況。

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

可用的資料表所示**結構描述**左側的索引標籤。 您可以找到下表中函式引動過程所產生的資料：

| 資料表 | 描述 |
| ----- | ----------- |
| **traces** | 執行階段和函式程式碼所建立的記錄檔。 |
| **requests** | 每個函式引動過程的一個要求。 |
| **exceptions** | 執行階段擲回任何例外狀況。 |
| **customMetrics** | 成功和失敗的引動過程、 成功率和持續時間的計數。 |
| **customEvents** | 執行階段，例如追蹤的事件：觸發函式的 HTTP 要求。 |
| **performanceCounters** | 伺服器上執行的函式的效能的相關資訊。 |

其他資料表是可用性測試和用戶端和瀏覽器遙測。 您可以實作自訂遙測，將資料新增至它們。

在每個資料表內，一些 Functions 特有的資料會位於 `customDimensions` 欄位中。  例如，下列查詢會擷取記錄層級為 `Error` 的所有追蹤。

```
traces 
| where customDimensions.LogLevel == "Error"
```

執行階段會提供`customDimensions.LogLevel`和`customDimensions.Category`欄位。 您可以提供您在您的函式程式碼中撰寫的記錄檔中的其他欄位。 請參閱本文稍後的[結構化記錄](#structured-logging)。

## <a name="configure-categories-and-log-levels"></a>設定類別和記錄層級

您可以使用 Application Insights，而不需要任何自訂設定。 預設組態會導致大量的資料。 如果您使用 Visual Studio Azure 訂用帳戶，可能就會達到 Application Insights 適用的資料上限。 稍後在本文中，您將了解如何設定及自訂您的函式傳送至 Application Insights 的資料。 函式應用程式中設定記錄[host.json]檔案。

### <a name="categories"></a>類別

Azure Functions 記錄器包括每個記錄的「類別」  。 類別指出寫入記錄的是哪個部分的執行階段程式碼或函式程式碼。 

Functions 執行階段建立的記錄檔與類別開頭為 「 主機 」。 「 函式開始，""函式已執行 」 及 「 函式已完成 」 記錄檔已分類為"Host.Executor"。 

如果您在您的函式程式碼中寫入記錄檔，其類別目錄會是"函式 」。

### <a name="log-levels"></a>記錄層級

也包含 Azure Functions 記錄器*記錄層級*與每個記錄檔。 [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) 為一個列舉，而整數代碼表示相對的重要性：

|LogLevel    |代碼|
|------------|---|
|追蹤       | 0 |
|偵錯       | 1 |
|資訊 | 2 |
|警告     | 3 |
|Error       | 4 |
|重要    | 5 |
|None        | 6 |

下一節會說明記錄層級 `None`。 

### <a name="log-configuration-in-hostjson"></a>在 host.json 中的記錄組態

[Host.json] 檔案會設定函式應用程式傳送到 Application Insights 的記錄數量。 針對每個類別，您可以指出要傳送的最小記錄層級。 有兩個範例： 第一個範例目標[函式版本 2.x 執行階段](functions-versions.md#version-2x)(.NET Core) 和第二個範例是針對版本 1.x 執行階段。

### <a name="version-2x"></a>2\.x 版

v2.x 版執行階段使用的是 [.NET Core 記錄篩選階層](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)。 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>1\.x 版

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

此範例會設定下列規則：

* 具有類別目錄的記錄檔`Host.Results`或是`Function`，只傳送`Error`層級和更新版本到 Application Insights。 `Warning` 層級和以下層級的記錄均會被忽略。
* 針對類別為 `Host.Aggregator` 的記錄，則會將所有記錄傳送到 Application Insights。 `Trace` 記錄層級和某些記錄器稱為 `Verbose` 的記錄層級相同，但會在 [host.json] 檔案中使用 `Trace`。
* 針對所有其他記錄，只會將 `Information` 層級和以上層級傳送至 Application Insights。

[Host.json] 中的類別值會控制以相同值開頭之所有類別的記錄。 `Host` 在  [host.json]記錄的控制項`Host.General`， `Host.Executor`， `Host.Results`，依此類推。

如果 [host.json] 包含多個以相同字串開頭的類別，則會先比對較長的類別。 假設您想要從執行階段以外的所有項目`Host.Aggregator`在登入`Error`層級，但您想要`Host.Aggregator`在登入`Information`層級：

### <a name="version-2x"></a>2\.x 版 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>1\.x 版 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

若要隱藏類別的所有記錄，您可以使用記錄層級 `None`。 沒有記錄檔會寫入與該類別，而且沒有任何記錄的層級。

下列各節說明執行階段建立之記錄的主要類別。 

### <a name="category-hostresults"></a>類別 Host.Results

這些記錄會在 Application Insights 中顯示為 "requests"。 它們表示函式的成功或失敗。

![要求圖表](media/functions-monitoring/requests-chart.png)

這些記錄全都寫入於`Information`層級。 如果您在篩選`Warning`或更新版本，不會看到任何資料。

### <a name="category-hostaggregator"></a>類別 Host.Aggregator

這些記錄會透過[可設定](#configure-the-aggregator)的期間來提供函式引動過程的計數與平均。 預設期間為 30 秒或 1,000 個結果，視何者較早達到而定。 

您可在 Application Insights 中的 **customMetrics** 資料表取得記錄。 範例包括執行、 成功率和持續時間的數目。

![customMetrics 查詢](media/functions-monitoring/custom-metrics-query.png)

這些記錄全都寫入於`Information`層級。 如果您在篩選`Warning`或更新版本，不會看到任何資料。

### <a name="other-categories"></a>其他類別

除了已經列出的類別之外，其他類別的所有記錄均可在 Application Insights 中的 **traces** 資料表內取得。

![追蹤查詢](media/functions-monitoring/analytics-traces.png)

所有的記錄檔開頭的類別與`Host`由 Functions 執行階段所撰寫。 「 函式已啟動 」 和 「 函式已完成 」 記錄檔有分類`Host.Executor`。 成功執行，這些記錄是`Information`層級。 例外狀況會記錄在`Error`層級。 執行階段也會建立 `Warning` 層級的記錄，例如：傳送至有害佇列的佇列訊息。

您的函式程式碼所寫入的記錄檔有分類`Function`而且可以是任何記錄層級。

## <a name="configure-the-aggregator"></a>設定彙總工具

如前一節所述，執行階段經過一段時間就會彙總有關函式執行的資料。 預設期間為 30 秒或 1,000 個執行，視何者較早達到而定。 您可以在 [host.json] 檔案中設定此設定。  以下是範例：

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>設定取樣

Application Insights 有[取樣](../azure-monitor/app/sampling.md)可以保護您免於上產生過多的遙測資料的功能已完成執行有時的尖峰負載。 連入的執行率超過指定的臨界值時，Application Insights 就會開始隨機忽略部分傳入的執行。 預設設定的每秒執行的最大數目為 20 (5 個版本 1.x)。 您可以在 [host.json] 中設定取樣。  以下是範例：

### <a name="version-2x"></a>2\.x 版 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>1\.x 版 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [取樣](../azure-monitor/app/sampling.md)預設為啟用。 如果您看起來會遺失資料，您可能需要調整取樣設定，以符合您特定的監視案例。

## <a name="write-logs-in-c-functions"></a>在 C# 函式中寫入記錄

您可以在函式程式碼中寫入記錄，其會在 Application Insights 中顯示為追蹤。

### <a name="ilogger"></a>ILogger

在您的函式中使用 [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) 參數而不是 `TraceWriter` 參數。 使用所建立的記錄檔`TraceWriter`移至 Application Insights，但`ILogger`可讓您執行[結構化記錄](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

利用 `ILogger` 物件，您可以呼叫 `Log<level>` [擴充方法 (位於 ILogger 上)](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) \(英文\) 來建立記錄。 下列程式碼會寫入`Information`記錄且類別為"Function"。

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>結構化記錄

預留位置的順序 (而不是名稱) 會決定要在記錄訊息中使用的參數。 假設您有下列程式碼：

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

如果您保留相同的訊息字串並反轉參數的順序，則產生的訊息文字值會處於錯誤的位置上。

您可以使用這種方式來處理預留位置，讓您能夠執行結構化記錄。 Application Insights 會儲存參數名稱 / 值組以及訊息字串。 結果就是訊息引數會變成您可以查詢的欄位。

如果記錄器方法呼叫看起來像上述範例中，您可以查詢該欄位`customDimensions.prop__rowKey`。 `prop__`以確保執行階段加入和欄位函式程式碼的欄位之間沒有衝突將新增前置詞。

您也可以藉由參考欄位 `customDimensions.prop__{OriginalFormat}`，在原始訊息字串上進行查詢。  

以下是 `customDimensions` 資料的範例 JSON 表示法：

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>自訂計量記錄

在 C# 指令碼函式中，您可以使用 `ILogger` 上的 `LogMetric` 擴充方法，在 Application Insights 中建立自訂計量。 以下是範例方法呼叫：

```csharp
logger.LogMetric("TestMetric", 1234);
```

此程式碼會於呼叫替代`TrackMetric`使用 Application Insights API for.NET。

## <a name="write-logs-in-javascript-functions"></a>在 JavaScript 函式中寫入記錄

在 Node.js 函式，使用 `context.log` 寫入記錄。 結構化的記錄未啟用。

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>自訂計量記錄

當您在執行[版本 1.x](functions-versions.md#creating-1x-apps) Node.js 函式可以使用 Functions 執行階段`context.log.metric`方法用來建立 Application Insights 中的自訂計量。 這個方法目前不支援版本 2.x。 以下是範例方法呼叫：

```javascript
context.log.metric("TestMetric", 1234);
```

此程式碼會於呼叫替代`trackMetric`使用 Application Insights Node.js SDK。

## <a name="log-custom-telemetry-in-c-functions"></a>登入的自訂遙測C#函式

您可以使用 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) \(英文\) NuGet 封裝，將自訂遙測資料傳送至 Application Insights。 以下 C# 範例使用的是[自訂遙測 API](../azure-monitor/app/api-custom-events-metrics.md)。 此範例適用於 .NET 類別庫，但 Application Insights 程式碼同樣適用於 C# 指令碼。

### <a name="version-2x"></a>2\.x 版

2\.x 版執行階段會使用 Application Insights 中的新功能，自動將遙測與目前作業相互關聯。 不需要手動設定作業`Id`， `ParentId`，或`Name`欄位。

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>1\.x 版

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

請不要呼叫`TrackRequest`或`StartOperation<RequestTelemetry>`因為您會看到函式引動過程的重複要求。  Functions 執行階段會自動追蹤要求。

請勿設定 `telemetryClient.Context.Operation.Id`。 許多函式會同時執行時，此全域設定會導致不正確的相互關聯。 請改為建立新的遙測執行個體 (`DependencyTelemetry`、`EventTelemetry`)，並修改其 `Context` 屬性。 接著，將遙測執行個體傳入至 `TelemetryClient` 上的對應 `Track` 方法 (`TrackDependency()`、`TrackEvent()`)。 這個方法可確保遙測具有目前的函式引動過程的正確的相互關聯詳細資料。

## <a name="log-custom-telemetry-in-javascript-functions"></a>JavaScript 函式中的記錄自訂遙測

[Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) 目前處於為搶鮮版 (Beta)。 以下是一些將自訂遙測傳送至 Application Insights 的範例程式碼：

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

`tagOverrides`參數集`operation_Id`函式的引動過程識別碼。 此設定能夠讓指定的函式引動過程中所有自動產生和自訂的遙測相互關聯。

## <a name="dependencies"></a>相依性

Functions v2 會自動收集 HTTP 要求、 服務匯流排，和 SQL 的相依性。

您可以撰寫自訂程式碼來顯示相依性。 如需範例，請參閱中的範例程式碼[C#自訂遙測區段](#log-custom-telemetry-in-c-functions)。 範例程式碼會產生*應用程式對應*在 Application Insights 中看起來像下列映像：

![應用程式對應](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>報告問題

若要回報關於 Functions 中 Application Insights 整合的問題，或是提出建議或要求，請[在 GitHub 中建立問題](https://github.com/Azure/Azure-Functions/issues/new) \(英文\)。

## <a name="streaming-logs"></a>串流記錄

開發應用程式時，如果能夠幾近即時地檢視記錄資訊，通常會很實用。 您可以檢視您在 Azure 入口網站中或在本機電腦上的命令列工作階段中的函式所產生的記錄檔的資料流。

這相當於看到當您偵錯您的函式期間的輸出[本機開發](functions-develop-local.md)。 如需詳細資訊，請參閱[如何串流處理記錄](../app-service/troubleshoot-diagnostic-logs.md#streamlogs)。

> [!NOTE]
> 串流記錄檔支援單一執行個體的 Functions 主機。 當您的函式會調整為多個執行個體時，來自其他執行個體的資料不會顯示在記錄資料流中。 [即時計量 Stream](../azure-monitor/app/live-stream.md) Application Insights 中會支援多個執行個體。 同時也在近乎即時，串流分析也基於[取樣的資料](#configure-sampling)。

### <a name="portal"></a>入口網站

若要在入口網站中檢視串流記錄檔，請選取**平台功能**函式應用程式中的索引標籤。 然後，在**監視**，選擇**記錄資料流**。

![啟用入口網站中的資料流記錄](./media/functions-monitoring/enable-streaming-logs-portal.png)

這會讓您的應用程式連線至記錄檔串流服務和應用程式記錄檔會顯示在視窗中。 您可以切換**應用程式記錄檔**並**Web 伺服器記錄**。  

![在入口網站中檢視串流記錄](./media/functions-monitoring/streaming-logs-window.png)

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="azure-cli"></a>Azure CLI

您可以使用，以啟用資料流記錄[Azure CLI](/cli/azure/install-azure-cli)。 若要登入，請選擇您的訂用帳戶和串流處理記錄檔中使用下列命令：

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

您可以使用，以啟用資料流記錄[Azure PowerShell](/powershell/azure/overview)。 針對 PowerShell，請使用下列命令來加入您的 Azure 帳戶，請選擇您的訂用帳戶和串流處理記錄檔：

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>停用內建記錄

當您啟用 Application Insights 時，停用內建使用 Azure 儲存體的記錄。 內建記錄適合用來測試少量的工作負載，但不適用於負載繁重的生產使用。 監控生產環境，我們建議使用 Application Insights。 如果在生產環境中使用內建的記錄，記錄可能會不完整因為 Azure 儲存體上的節流。

若要停用內建記錄，請刪除 `AzureWebJobsDashboard` 應用程式設定。 如需在 Azure 入口網站中刪除應用程式設定的相關資訊，請參閱[如何管理函式應用程式](functions-how-to-use-azure-function-app-settings.md#settings)的**應用程式設定**。 刪除應用程式設定之前，請確定沒有現有的函式，在相同的函式應用程式中使用的 Azure 儲存體觸發程序或繫結的設定。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core 記錄](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
