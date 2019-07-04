---
title: 智慧型偵測 - Application Insights 中的失敗異常 | Microsoft Docs
description: 針對 Web 應用程式失敗要求比率的不尋常變化對您發出警示，並提供診斷分析。 不需要設定。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: 46944603fdf45a2a7a14641086959bf61b3f773e
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465876"
---
# <a name="smart-detection---failure-anomalies"></a>智慧型偵測 - 失敗異常
當 Web 應用程式的失敗要求比率異常增加時，[Application Insights](../../azure-monitor/app/app-insights-overview.md) 會以幾乎即時的方式自動通知您。 它偵測到回報為失敗的 HTTP 要求率異常提高或相依性呼叫。 對於要求，失敗的要求通常是回應碼為 400 或更高的要求。 為了協助您分級並診斷問題，通知中會提供失敗的特性分析與相關遙測。 其中也有 Application Insights 入口網站的連結，以供進一步診斷。 不需要設定該功能，因為它是使用機器學習演算法來預測一般失敗率。

此功能適用於任何 web 應用程式，裝載在雲端中，或您自己的伺服器上產生要求或相依性遙測-例如，如果您有呼叫的背景工作角色[trackrequest （)](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)或[trackdependency （)](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

設定[專案的 Application Insights](../../azure-monitor/app/app-insights-overview.md)之後，如果您的應用程式產生某些最少量的遙測，錯誤異常的「智慧型偵測」需先花費 24 小時來了解您應用程式的正常行為，然後才會啟動而能夠傳送警示。

以下是警示範例。

![顯示失敗相關叢集分析的範例智慧型偵測警示](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> 根據預設，您所取得的郵件格式會比這個範例還簡短。 不過，您也可以 [切換到這個詳細格式](#configure-alerts)。
>
>

請注意，它會告訴您︰

* 相較於一般應用程式行為的失敗率。
* 受影響的使用者人數 - 讓您知道應處理的數目。
* 與失敗關聯的特性模式。 在此範例中，有特定的回應碼、要求名稱 (操作) 和應用程式版本。 可立即告訴您從何處開始了解程式碼。 其他可能性則可能是特定的瀏覽器或用戶端作業系統。
* 看起來似乎與特色失敗相關聯的例外狀況、記錄追蹤和相依性失敗 (資料庫或其他外部元件)。
* 直接連結到 Application Insights 的遙測上相關搜尋。

## <a name="failure-anomalies-v2"></a>失敗異常 v2
現在使用新版的失敗異常警示規則。 這個新版本在新的 Azure 警示平台上執行，並覆蓋現有版本介紹各種不同的增強功能。

### <a name="whats-new-in-this-version"></a>什麼是此版本的新功能？
- 更快速偵測問題
- 警示規則會建立一組豐富的動作，以及相關聯[動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)名為"Application Insights 智慧偵測 」，包含電子郵件和 webhook 動作，而且可以擴充來觸發其他動作時警示就會引發。
- 集中精力通知-此警示規則所傳來的電子郵件通知現在依預設會傳送給訂用帳戶的監視讀取器 」 和 「 監視參與者 」 角色相關聯的使用者。 在此的詳細資訊[此處](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)。
- 更容易的設定，透過 ARM 範本-請參閱範例[此處](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)。
- 常見的警示的結構描述支援-請遵循此警示規則所傳來的通知[常見的警示結構描述](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)。
- 整合電子郵件範本-電子郵件通知此警示規則已一致的外觀與感覺與其他警示類型。 透過這項變更，以取得詳細的診斷資訊的失敗異常警示選項不再可用。

### <a name="how-do-i-get-the-new-version"></a>如何取得新版本？
- 新建立的 Application Insights 資源現在會使用新的版本，失敗異常警示規則的佈建。
- 現有的 Application Insights 資源的精簡版本的失敗異常警示規則將會取得新版本一次其裝載的訂用帳戶移轉至新的警示平台一部分[傳統警示淘汰程序](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> 失敗異常警示規則的新版本仍為可用。 此外，電子郵件和 webhook 觸發動作由相關聯 「 Application Insights 智慧偵測 」 也是免費的動作群組。
> 
> 

## <a name="benefits-of-smart-detection"></a>智慧型偵測的優點
一般的 [度量警示](../../azure-monitor/app/alerts.md) 會告訴您可能有問題。 但「智慧型偵測」會為您啟動診斷工作，執行許多您原本必須自己執行的分析。 您達到幾近封裝完成的結果，幫助您快速取得問題的根源。

## <a name="how-it-works"></a>運作方式
「智慧型偵測」會監視從應用程式收到的遙測，特別是失敗率的遙測。 此規則會計算 `Successful request` 屬性為 false 的要求數，以及 `Successful call` 屬性為 false 的相依性呼叫數。 對於要求，根據預設，`Successful request == (resultCode < 400)` (除非您撰寫自訂程式碼來[篩選](../../azure-monitor/app/api-filtering-sampling.md#filtering)或產生自己的 [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 呼叫)。 

您的應用程式效能具有一般的行為模式。 某些要求或相依性呼叫會比其他要求更容易失敗；且整體失敗率可能會隨著負載增加而上移。 「智慧型偵測」會使用機器學習服務來尋找這些異常狀況。

當遙測從您的 Web 應用程式進入 Application Insights 時，「智慧型偵測」會將目前的行為與過去幾天觀察到的模式做比較。 如果觀察到與先前效能相比，失敗率異常提高，就會觸發分析。

觸發分析時，服務會對失敗的要求執行叢集分析，以嘗試識別描述失敗特徵之值的模式。 在上述範例中，分析已發現大部分失敗是關於特定結果碼、要求名稱、伺服器 URL 主機和角色執行個體。 相反地，分析發現，用戶端作業系統屬性分散在多個值，因此未列出。

當您的服務使用這些遙測呼叫進行檢測時，分析器會尋找與已識別叢集中要求建立關聯的例外狀況及相依性失敗，同時也包含與那些要求建立關聯的任何追蹤記錄範例。

產生的分析報告會以警示寄送給您，除非您已設定不接收該報告。

如同 [您手動設定的警示](../../azure-monitor/app/alerts.md)，您可以檢查警示的狀態，並在 Application Insights 資源的 [警示] 刀鋒視窗中進行設定。 但與其他警示不同，您並不需要設定「智慧型偵測」。 若有需要，您可以將它停用或變更其目標電子郵件地址。

### <a name="alert-logic-details"></a>警示邏輯詳細資料

這些警示是由我們專屬的機器學習演算法所觸發，因此我們無法共用確切的實作詳細資料。 不過，我們知道您有時需要深入了解基礎邏輯的運作方式。 評估以判斷是否應觸發警示的主要因素如下： 

* 分析要求/相依性在 20 分鐘累積時間範圍內的失敗百分比。
* 比較過去 20 分鐘的失敗百分比與過去 40 分鐘和過去七天的比率，以及尋找超過標準差 X 倍的重大偏差。
* 針對最小失敗百分比使用自動調整限制，此限制會依據要求/相依性的應用程式磁碟區而有所不同。

## <a name="configure-alerts"></a>設定警示
您可以停用「智慧型偵測」、變更電子郵件收件者、建立 Webhook，或選擇接收更詳細的警示訊息。

開啟 [警示] 頁面。 「失敗異常」已包含在任何您已手動設定的警示中，您可以查看它目前是否處於警示狀態。

![在 [概觀] 頁面中，按一下 [警示] 圖格。 或在任何 [度量] 頁面上，按一下 [警示] 按鈕。](./media/proactive-failure-diagnostics/021.png)

按一下警示來進行設定。

![組態](./media/proactive-failure-diagnostics/032.png)

請注意，您可以停用「智慧型偵測」，但無法將它刪除 (或建立另一個「智慧型偵測」)。

#### <a name="detailed-alerts"></a>詳細的警示
如果您選取 [取得更詳細的診斷資料]，則電子郵件會包含詳細的診斷資訊。 有時候您從電子郵件中的資料就能夠診斷問題。

因為更詳細的警示包含例外狀況和追蹤訊息，所以不太可能包含機密資訊。 不過，只有當您的程式碼允許機密資訊進入這些訊息時，才會發生。

## <a name="triaging-and-diagnosing-an-alert"></a>分級和診斷警示
發出警示表示系統偵測到要求失敗率異常上升。 原因可能是您的應用程式或其環境有問題。

您可以根據要求的百分比及受影響的使用者數目來決定此問題的緊急程度。 在上述範例中，22.5% 的失敗率與正常比率 1% 相比，表示有問題。 從另一方面來看，只有 11 位使用者受到影響。 如果是您的應用程式，您可以評估嚴重程度。

在多數情況下，您可以根據要求名稱、例外狀況、相依性失敗及提供的追蹤資料來快速診斷問題。

另有一些線索。 例如，此範例中的相依性失敗率與例外狀況率 (89.3%) 相同。 這可能表示例外狀況直接從相依性失敗產生，讓您清楚瞭解從何處開始查看程式碼。

若要進一步調查，每個區段中的連結將直接連結到已針對相關要求、例外狀況、相依性或追蹤篩選的 [搜尋頁面](../../azure-monitor/app/diagnostic-search.md) 。 或者，您可以開啟 [Azure 入口網站](https://portal.azure.com)，瀏覽至應用程式的 Application Insights 資源，並開啟 [失敗] 刀鋒視窗。

在此範例中，按一下 [檢視相依性失敗詳細資料] 連結會開啟 [Application Insights 搜尋] 刀鋒視窗。 它會顯示具有根本原因範例的 SQL 陳述式︰必要欄位會提供 Null，而且在儲存作業過程中未通過驗證。

![診斷搜尋](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>檢閱最近的警示

按一下 [智慧型偵測]  來移至最新的警示：

![警示摘要](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>不同之處在於...
失敗異常的「智慧型偵測」可以與其他相似但不同的 Application Insights 功能互補。

* 您可以設定[度量警示](../../azure-monitor/app/alerts.md)，且可以檢視各種度量，例如 CPU 使用量、要求率、頁面載入時間等等。 您可以使用它們來向自己發出警告，例如，如果您需要增加更多資源時。 對比之下，失敗異常的「智慧型偵測」只涵蓋小範圍的重要度量 (目前僅包含要求失敗率)，其設計目的是要在 Web 應用程式的要求失敗率與其正常行為相比有明顯增加時，能以幾乎即時的方式通知您。

    「智慧型偵測」會自動因應主要條件來調整其臨界值。

    「智慧型偵測」會為您啟動診斷工作。
* [效能異常的智慧型偵測](proactive-performance-diagnostics.md)也會使用機器智慧在您的度量中探索不尋常的模式，且您不需要進行任何設定。 但與失敗異常的「智慧型偵測」不同，效能異常的「智慧型偵測」目的是要尋找您各種使用方式中未獲得正確處理的片段，例如未獲得特定瀏覽器上特定的頁面正確處理。 此分析會每日執行，且如果發現任何結果，它可能不像警示那麼緊急。 對比之下，失敗異常的分析則是會對傳入的遙測持續執行，且如果伺服器失敗率超出預期，您就會在幾分鐘內收到通知。

## <a name="if-you-receive-a-smart-detection-alert"></a>如果您收到「智慧型偵測」警示
*為什麼會收到這個警示？*

* 我們偵測到要求失敗率相較於前一個期間正常基準異常上升。 在分析失敗和關聯的遙測後，我們認為您應該注意發生的問題。

*收到通知代表一定是有問題嗎？*

* 我們嘗試針對應用程式中斷或效能降低發出警示，但只有您可以完全了解語意和對應用程式或使用者的影響。

*所以你們會看到我的資料嗎？*

* 沒有。 服務完全是自動的。 只有您會收到通知。 您的資料是 [不公開的](../../azure-monitor/app/data-retention-privacy.md)。

*我是否必須訂閱此警示？*

* 沒有。 每個傳送要求遙測的應用程式都有「智慧型偵測」警示規則。

*我是否可以取消訂閱或改為傳送通知給我的同事？*

* 是，請在 [警示] 規則中，按一下 [智慧型偵測] 規則來設定它。 您可以停用警示，或變更警示的收件者。

*我遺失了電子郵件。在入口網站中哪裡可以找到通知？*

* 在 Azure 記錄中。 在 Azure 中，開啟您應用程式的 Application Insights 資源，然後選取 [活動] 記錄。

*部分警示與已知問題相關，我不想接收它們。*

* 我們已在稽核記錄檔中抑制警示。

## <a name="next-steps"></a>後續步驟
這些診斷工具可協助您檢查來自您的應用程式的遙測︰

* [計量瀏覽器](../../azure-monitor/app/metrics-explorer.md)
* [搜尋總管](../../azure-monitor/app/diagnostic-search.md)
* [分析 - 功能強大的查詢語言](../../azure-monitor/log-query/get-started-portal.md)

智慧型偵測是完全自動的。 但是，或許您會想要再設定一些警示？

* [手動設定的度量警示](../../azure-monitor/app/alerts.md)
* [可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md)
