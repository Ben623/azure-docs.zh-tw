---
title: 實作搜尋流量分析 - Azure 搜尋服務
description: 為「Azure 搜尋服務」啟用搜尋流量分析，以將遙測和使用者起始的事件新增到記錄檔。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b15ae30151b22509a78b9a39d258991363a05e5b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295429"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>在 Azure 搜尋服務中實作搜尋流量分析
搜尋流量分析是一種模式，可用來實作搜尋服務的意見反應管道。 這個模式描述必要的資料，以及如何使用監視多平台服務中的業界領導者 Application Insights 來加以收集。

搜尋流量分析可讓您掌握您的搜尋服務，並深入分析您的使用者及其行為。 擁有使用者選擇項目的相關資料，您所做的決定就能夠進一步改善搜尋體驗，並且在結果不如預期時予以中斷。

Azure 搜尋服務所提供的遙測解決方案可整合 Azure Application Insights 和 Power BI，以提供深入的監視和追蹤。 因為只能透過 API 與 Azure 搜尋服務進行互動，必須遵循這個頁面中的指示，由開發人員使用搜尋將遙測進行實作。

## <a name="identify-relevant-search-data"></a>識別相關的搜尋資料

如需取得實用的搜尋計量，就必須從搜尋應用程式的使用者記錄一些訊號。 這些訊號代表使用者感興趣且認為與其需求相關的內容。

搜尋流量分析需要兩個訊號︰

1. 使用者產生的搜尋事件︰僅使用者所起始的搜尋查詢才有趣。 用來填入 Facet、其他內容或任何內部資訊的搜尋要求並不重要，而且還會扭曲和偏差結果。

2. 使用者產生的按一下事件：透過在此文件中按一下，我們意指選取搜尋查詢所傳回之特定搜尋結果的使用者。 點選通常表示文件為特定搜尋查詢的相關結果。

使用相互關聯的識別碼將搜尋和點選事件進行連結，就可以分析您應用程式上的使用者行為。 僅使用搜尋流量記錄並無法取得這些搜尋深入分析。

## <a name="add-search-traffic-analytics"></a>新增搜尋流量分析

必須從搜尋應用程式收集前一節所述的訊號，因為使用者會與其進行互動。 Application Insights 是可延伸的監視解決方案，適用於多個平台，並具有彈性的檢測選項。 使用 Application Insights 可讓您充分利用 Azure 搜尋服務所建立的 Power BI 搜尋報告，使資料分析更輕鬆。

在 Azure 搜尋服務的[入口網站](https://portal.azure.com)頁面中，搜尋流量分析刀鋒視窗包含了遵循此遙測模式的小祕技。 您也可以選取或建立 Application Insights 資源，並查看必要的資料，這些全都放在同一個位置。

![搜尋流量分析指示][1]

## <a name="1---select-a-resource"></a>1 - 選取資源

如果您還沒有 Application Insights 資源，則必須選取 Application Insights 資源後，才可以加以使用或建立。 您可以使用已在使用中的資源，才可記錄必要的自訂事件。

在建立新的 Application Insights 資源時，所有應用程式類型都適用於這種情況。 選取最適合您所使用平台的 Application Insights 資源。

您需要檢測金鑰來建立您應用程式的遙測用戶端。 您可以從 Application Insights 入口網站儀表板取得，或可以在搜尋流量分析頁面上，選取您想要使用的執行個體來加以取得。

## <a name="2---add-instrumentation"></a>2 - 新增檢測

在此階段中，您可以使用上述步驟中建立的 Application Insights 資源，將自己的搜尋應用程式進行檢測。 這個程序有四個步驟︰

**步驟 1：建立用戶端遙測** 這是將事件傳送至 Application Insights 資源的物件。

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

如需了解其他語言和平台，請參閱完整的[清單](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)。

**步驟 2：要求相互關聯的搜尋識別碼** 若要透過點選將搜尋要求相互關聯，則必須擁有與這兩個不同事件相關的相互關聯識別碼。 當您使用標頭進行要求時，Azure 搜尋服務會為您提供搜尋識別碼︰

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**步驟 3：記錄搜尋事件**

每當使用者發出搜尋要求時，您應該將其記錄為搜尋事件，並包含下列關於 Application Insights 自訂事件的結構描述︰

**SearchServiceName**: （字串） 搜尋服務名稱**SearchId**: (guid) 的搜尋查詢的唯一識別碼 （出現在搜尋回應中） **IndexName**: （字串） 搜尋服務索引查詢**QueryTerms**: （字串） 使用者輸入的搜尋字詞**ResultCount**: (int) 所傳回的文件數目 （出現在搜尋回應中） **ScoringProfile**： 如果有任何使用評分設定檔名稱 （字串）

> [!NOTE]
> 將 $count=true 新增至搜尋查詢，要求使用者所產生查詢的計數。 請在[這裡](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)參閱詳細資訊
>

> [!NOTE]
> 請記得，務必只記錄使用者所產生的搜尋查詢。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**步驟 4：記錄點選事件**

每當使用者點選文件時，必須記錄這個訊號才可進行搜尋分析。 使用 Application Insights 自訂事件，記錄包含下列結構描述的這些事件：

**ServiceName**：(字串) 搜尋服務名稱 **SearchId**：(GUID) 相關搜尋查詢的唯一識別碼 **DocId**：(字串) 文件識別碼 **Position**(int) 文件在搜尋結果頁面中的排名

> [!NOTE]
> 位置是指您應用程式中的基本順序。 您可以自由地設定這個數字 (只要它一律相同) 以便進行比較。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - 在 Power BI 中分析

在您完成檢測您的應用程式，並確認您的應用程式正確地連線至 Application Insights 之後，可以使用 Azure 搜尋服務針對 Power BI Desktop 所建立的預先定義範本。 

「Azure 搜尋服務」會提供監視 [Power BI 內容套件](https://app.powerbi.com/getdata/services/azure-search)，以便讓您分析記錄資料。 此內容套件會新增預先定義的圖表和資料表，可用來分析為搜尋流量分析擷取的額外資料。 如需詳細資訊，請參閱[內容套件說明網頁](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/)。 

1. 在「Azure 搜尋服務」儀表板左側導覽窗格的 [設定]  底下，按一下 [搜尋流量分析]  。

2. 在 [搜尋流量分析]  頁面上的步驟 3 中，按一下 [取得 Power BI Desktop]  以安裝 Power BI。

   ![取得 Power BI 報表](./media/search-traffic-analytics/get-use-power-bi.png "取得 Power BI 報表")

2. 在相同頁面上，按一下 [下載 PowerBI 報表]  。

3. 報表會在 Power BI Desktop 中開啟，系統會提示您連線至 Application Insights。 您可以在 Azure 入口網站的 Application Insights 資源頁面中找到此資訊。

   ![連線至 Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "連線至 Application Insights")

4. 按一下 [載入]  。

此報表包含圖表和資料表，可幫助您做出更明智的決策，以改善您的搜尋效能和相關性。

計量包含下列項目：

* 點選率 (CTR)：點選特定文件的使用者數與總搜尋數的比率。
* 無點選搜尋︰註冊無點選的熱門查詢詞彙
* 最多點選的文件︰過去 24 小時、7 天和 30 天內，依識別碼排序最多點選的文件。
* 常用的詞彙文件組︰依點選排序，造成點選相同文件的詞彙。
* 點選的時間︰自搜尋查詢起按照時間分組的點選

以下螢幕擷取畫面示範用於分析搜尋流量分析的內建報表和圖表。

![Azure 搜尋服務的 Power BI 儀表板](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Azure 搜尋服務的 Power BI 儀表板")

## <a name="next-steps"></a>後續步驟
檢測您的搜尋應用程式，取得搜尋服務的強大且詳細相關資料。

您可以在 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 上找到詳細資訊，以及瀏覽[價格頁面](https://azure.microsoft.com/pricing/details/application-insights/)頁面來深入了解其不同的服務層級。

深入了解如何建立令人讚嘆的報告。 如需詳細資訊，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
