---
title: 監視搜尋服務的資源使用量和查詢計量 - Azure 搜尋服務
description: 從「Azure 搜尋服務」啟用記錄功能、取得查詢活動計量、資源使用量及其他系統資料。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bac897178c8220abe72a92a5cf14fc4767cdd3bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755069"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>監視 Azure 搜尋服務中的資源耗用量和查詢活動

在您「Azure 搜尋服務」的 [概觀] 頁面中，您可以檢視有關資源使用量、查詢計量及有多少配額可供建立更多索引、索引子和資料來源的系統資料。 您也可以使用入口網站來設定記錄分析，或另一個用於持續性資料收集的資源。 

設定記錄對於自我診斷和保留操作歷程記錄相當有用。 就內部而言，記錄會短暫地存在於後端，足以在您提出支援票證時應付調查和分析。 如果您想要控制和存取記錄資訊，您應該設定本文中所述的其中一個解決方案。

在本文中，您會了解監視選項、如何啟用記錄功能和記錄儲存，以及如何檢視記錄內容。

## <a name="metrics-at-a-glance"></a>計量一目了然

[概觀] 頁面中內建的 [使用量]  和 [監視]  區段會報告資源耗用量和查詢執行計量。 在您開始使用此服務之後，此資訊就會立即可用，無須進行任何設定。 此頁面每隔幾分鐘就會重新整理一次。 如果您要完成有關[要針對生產環境工作負載使用哪個定價層](search-sku-tier.md)或是否要[調整作用中複本和分割區的數量](search-capacity-planning.md)的決策，這些計量可透過顯示資源消耗速度及目前設定處理現有負載的情況，協助您進行這些決策。

[使用量]  索引標籤會顯示相對於目前[限制](search-limits-quotas-capacity.md)的資源可用性。 以下是一個免費服務的圖例，其上限為每個類型 3 個物件且儲存體為 50 MB。 「基本」或「標準」服務的上限較高，且如果您增加分割區計數，儲存體上限也會按比例增加。

![相對於有效限制的使用量狀態](./media/search-monitor-usage/usage-tab.png
 "相對於有效限制的使用量狀態")

## <a name="queries-per-second-qps-and-other-metrics"></a>每秒查詢數目 (QPS) 及其他計量

[監視]  索引標籤會顯示計量 (例如「每秒查詢數目」  (QPS)) 的移動平均，每分鐘彙總一次。 
「搜尋延遲」  是搜尋服務處理搜尋查詢所需的時間，每分鐘彙總一次。 「已節流的搜尋查詢百分比」  (未顯示) 是已節流的搜尋查詢百分比，也是每分鐘彙總一次。

這些數字是近似值，目的是要讓您概略了解您系統為要求提供服務的情況。 實際 QPS 可能比入口網站中回報的數字更高或更低。

![每秒查詢數目活動](./media/search-monitor-usage/monitoring-tab.png "每秒查詢數目活動")

## <a name="activity-logs"></a>活動記錄

[活動記錄]  會從 Azure Resource Manager 收集資訊。 可在 [活動記錄] 中找到的資訊範例包括建立或刪除服務、更新資源群組、檢查名稱可用性，或是取得用以處理要求的服務存取金鑰。 

您可以從左側導覽窗格、從頂端視窗命令列中的 [通知] 或從 [診斷並解決問題]  頁面，存取 [活動記錄]  。

針對服務內的工作 (例如建立索引或刪除資料來源)，您會看到每個要求的一般通知 (例如「取得管理員金鑰」)，但不會看到特定動作本身。 針對此層級的資訊，您必須啟用附加的監視解決方案。

## <a name="add-on-monitoring-solutions"></a>附加的監視解決方案

「Azure 搜尋服務」不會儲存所管理物件以外的任何資料，這表示記錄資料必須儲存在外部。 如果您想要保存記錄資料，可以設定下方任何資源。 

下表將儲存記錄與透過 Application Insights 新增服務作業和查詢工作負載之深入監視的選項做比較。

| Resource | 用於 |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | 記錄的事件和查詢度量，根據下列結構描述與您的應用程式中的使用者事件相互關聯。 這是唯一會將使用者動作或信號列入考量的解決方案，其中會將來自使用者所起始搜尋的事件與應用程式碼所提交的篩選要求對應。 若要使用此方法，請複製檢測程式碼並貼到您的原始程式檔中，以將要求資訊路由傳送至 Application Insights。 如需詳細資訊，請參閱[搜尋流量分析](search-traffic-analytics.md)。 |
| [Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | 記錄的事件和查詢度量，根據下列結構描述。 事件會記錄到 Log Analytics 工作區。 您可以對工作區執行查詢，以從記錄傳回詳細的資訊。 如需詳細資訊，請參閱[開始使用 Azure 監視器記錄檔](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 記錄的事件和查詢度量，根據下列結構描述。 事件會記錄至 Blob 容器並儲存在 JSON 檔案中。 請使用 JSON 編輯器來檢視檔案內容。|
| [事件中樞](https://docs.microsoft.com/azure/event-hubs/) | 根據本文中記載的結構描述，記錄事件和查詢計量。 請選擇此選項作為非常大型記錄的替代資料收集服務。 |

Azure 監視器記錄檔和 Blob 儲存體是免費的共用服務的形式提供，以便您可以試用免費的 Azure 訂用帳戶的存留期。 Application Insights 可供免費註冊和使用，只要應用程式資料大小在特定限制範圍內即可 (如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/monitor/))。

下一節將逐步解說啟用及使用 Azure Blob 儲存體來收集和存取「Azure 搜尋服務」作業所建立記錄資料的步驟。

## <a name="enable-logging"></a>啟用記錄

索引編製和查詢工作負載的記錄功能預設為關閉，並且取決於記錄功能基礎結構和長期外部儲存體的附加解決方案。 單獨就「Azure 搜尋服務」而言，其唯一保存的資料是它所建立和管理的物件，因此記錄必須儲存在其他位置。

在本節中，您將了解如何使用 Blob 儲存體來儲存所記錄的事件和計量資料。

1. 如果您還沒有儲存體帳戶，請[建立一個儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 您可以將它放在與「Azure 搜尋服務」相同的資源群組中，以在稍後當您想要刪除本練習中使用的所有資源時，可簡化清除步驟。

   您的儲存體帳戶必須存在於 Azure 搜尋服務與相同的區域。

2. 開啟您的搜尋服務 [概觀] 頁面。 在左側導覽窗格中，向下捲動至 [監視]  ，然後按一下 [啟用監視]  。

   ![啟用監視](./media/search-monitor-usage/enable-monitoring.png "啟用監視")

3. 選擇您想要匯出的資料：記錄、計量或兩者。 您可以將它複製到儲存體帳戶、 將它傳送到事件中樞，或將它匯出至 Azure 監視器記錄檔。

   若要封存至 Blob 儲存體，只有儲存體帳戶必須存在。 容器和 blob 會視需要時建立記錄檔資料會匯出。

   ![設定 Blob 儲存體封存](./media/search-monitor-usage/configure-blob-storage-archive.png "設定 Blob 儲存體封存")

4. 儲存設定檔。

5. 藉由建立或刪除物件 (會建立記錄事件) 或提交查詢 (會產生計量) 來測試記錄功能。 

記錄功能在您儲存設定檔後便會啟用。 只有在有活動可供記錄或測量時，才會建立容器。 將資料複製到儲存體帳戶時，資料會格式化為 JSON 並放在兩個容器中︰

* insights-logs-operationlogs：適用於搜尋流量記錄
* insights-metrics-pt1m：適用於計量

**需要一個小時的時間才容器會出現在 Blob 儲存體。沒有一個 blob，每小時、 每個容器。**

您可以使用 [Visual Studio Code](#download-and-open-in-visual-studio-code) 或另一個 JSON 編輯器還檢視檔案。 

### <a name="example-path"></a>範例路徑

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>記錄檔結構描述
包含您搜尋服務流量記錄的 Blob 會結構化，如本節所述。 每個 Blob 都有一個名為**記錄**的根物件，其中包含記錄物件的陣列。 每個 Blob 都包含在同一小時內發生之所有作業的記錄。

| 名稱 | type | 範例 | 注意 |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的 ResourceId |
| operationName |string |"Query.Search" |作業的名稱 |
| operationVersion |string |"2019-05-06" |使用的 api-version |
| category |string |"OperationLogs" |常數 |
| resultType |string |"Success" |可能的值：成功或失敗 |
| resultSignature |ssNoversion |200 |HTTP 結果碼 |
| durationMS |ssNoversion |50 |作業的持續時間 (以毫秒為單位) |
| properties |object |請參閱下表 |包含作業特定資料的物件 |

**屬性結構描述**

| 名稱 | type | 範例 | 注意 |
| --- | --- | --- | --- |
| 描述 |string |"GET /indexes('content')/docs" |作業的端點 |
| 查詢 |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |查詢參數 |
| 文件 |ssNoversion |42 |處理的文件數目 |
| IndexName |string |"testindex" |與作業相關聯的索引名稱 |

## <a name="metrics-schema"></a>度量結構描述

針對查詢要求，會擷取計量。

| 名稱 | type | 範例 | 注意 |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的資源識別碼 |
| metricName |string |"Latency" |度量的名稱 |
| 分析 |datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| average |ssNoversion |64 |度量時間間隔中原始範例的平均值 |
| minimum |ssNoversion |37 |度量時間間隔中原始範例的最小值 |
| maximum |ssNoversion |78 |度量時間間隔中原始範例的最大值 |
| total |ssNoversion |258 |度量時間間隔中原始範例的總和值 |
| count |ssNoversion |4 |用來產生度量的原始樣本數 |
| timegrain |string |"PT1M" |採用 ISO 8601 的度量時間粒紋 |

每隔一分鐘就會回報所有計量。 每個度量會顯示每分鐘的最小值、最大值和平均值。

以 SearchQueriesPerSecond 度量來說，最小值是該分鐘內已註冊的每秒搜尋查詢次數最低值。 最大值依此類推。 平均值是一分鐘的彙總。
假設一分鐘內有這種情況：有 1 秒出現極高的負載，這是 SearchQueriesPerSecond 的最大值，接著有 58 秒的平均負載，最後的 1 秒只有一個查詢，而這會是最小值。

對於 ThrottledSearchQueriesPercentage，最小值、最大值、平均值和總計全是相同的值：在一分鐘內的搜尋查詢總數中，已節流處理的搜尋查詢百分比。

## <a name="download-and-open-in-visual-studio-code"></a>下載並在 Visual Studio Code 中開啟

您可以使用任何 JSON 編輯器來檢視記錄檔。 如果您沒有編輯器，建議您使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 入口網站中，開啟您的儲存體帳戶。 

2. 在左側導覽窗格中，按一下 [Blob]  。 您應該會看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 這些容器是將記錄資料匯出至 Blob 儲存體時，「Azure 搜尋服務」所建立的容器。

3. 在資料夾階層中一路往下點選，直到抵達 .json 檔案為止。  請使用操作功能表來下載檔案。

下載檔案之後，在 JSON 編輯器中開啟它以檢視內容。

## <a name="use-system-apis"></a>使用系統 API
「Azure 搜尋服務」REST API 和 .NET SDK 都可讓您以程式設計方式存取服務計量、索引和索引子資訊，以及文件計數。

* [取得服務統計資料](/rest/api/searchservice/get-service-statistics)
* [取得索引統計資料](/rest/api/searchservice/get-index-statistics)
* [文件計數](/rest/api/searchservice/count-documents)
* [取得索引子狀態](/rest/api/searchservice/get-indexer-status)

若要使用 PowerShell 或 Azure CLI 來啟用，請參閱[這裡](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)的文件。

## <a name="next-steps"></a>後續步驟

如需有關服務管理的詳細資訊，請參閱[在 Microsoft Azure 上管理搜尋服務](search-manage.md)，如需調整指引，請參閱[效能與最佳化](search-performance-optimization.md)。