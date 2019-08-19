---
title: 管理 Azure 監視器記錄的使用量和成本 |Microsoft Docs
description: 瞭解如何在 Azure 監視器中變更 Log Analytics 工作區的定價方案和管理資料量和保留原則。
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 5e325f7766e7b0d9764949eb3fbf9753d65db8b3
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619388"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure 監視器記錄來管理使用量和成本

> [!NOTE]
> 本文說明如何藉由設定 Log Analytics 工作區的資料保留期限, 在 Azure 監視器中控制成本。  如需相關資訊, 請參閱下列文章。
> - [監視使用量和估計成本](usage-estimated-costs.md)說明如何針對不同的定價模型，檢視多項 Azure 監視功能的使用量和估計成本。 其中也會說明如何變更定價模型。

Azure 監視器記錄是設計用來調整和支援每天從企業中的任何來源收集、編制索引和儲存大量資料, 或部署在 Azure 中。  雖然這可能是您組織的主要推動力，但成本效率終究是最基本的推動力。 為此, 請務必瞭解 Log Analytics 工作區的成本不是根據所收集的資料量, 也取決於選取的方案, 以及您選擇儲存從連線來源產生之資料的時間長度。  

在本文中，我們會探討您可以如何積極地監視資料量和儲存體成長狀況，並定義限制來控制這些相關成本。 

資料成本是否會變得可觀取決於下列因素： 

- 所產生的資料量和內嵌至工作區的資料量 
    - 已啟用的管理解決方案數量
    - 受監視的系統數量
    - 從每個受監視資源收集的資料類型 
- 您決定要保留資料的時間長度 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>了解工作區的使用量和估計成本

Azure 監視器記錄可讓您根據最近的使用模式, 輕鬆地瞭解可能的成本。 若要這麼做, 請使用**Log Analytics 使用量和估計成本**來審查和分析資料使用量。 這能顯示每個解決方案所收集的資料量、所保留的資料量，以及根據所擷取的資料量及超出所包含量之任何額外保留資料量所進行的成本評估。

![使用方式和估計成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更詳細地探索您的資料，請在 [使用量和估計成本] 頁面上，按一下位於任一圖表右上方的圖示。 現在您可以使用此查詢來探索更詳細的使用量。  

![記錄檢視](media/manage-cost-storage/logs.png)

您可以從 [使用量和估計成本] 頁面檢閱當月的資料量。 這之中包括 Log Analytics 工作區中接收和保留的所有資料。  按一下頁面頂端的 [**使用量詳細資料**], 以依據來源、電腦和供應專案, 使用資料量趨勢的資訊來查看使用量儀表板。 若要檢視和設定每日上限或修改保留期限，請按一下 [資料量管理]。
 
Log Analytics 費用會新增到您的 Azure 帳單中。 您可以在 Azure 入口網站中的 [帳務] 區段下，或在 [Azure 計費入口網站](https://account.windowsazure.com/Subscriptions)中，查看 Azure 帳單的詳細資料。  

## <a name="daily-cap"></a>每日上限

您可以為工作區設定每日上限和限制每日擷取，但請謹慎使用，因為您的目標是不要達到每日限制。  否則，您會遺失當天其餘時間的資料，這可能會影響其功能取決於工作區中一直在提供的最新資料的其他 Azure 服務和解決方案。  如此一來，您在資源健全狀況支援 IT 服務時觀察和接收警示的功能會受到影響。  每日上限是用來管理受控資源中非預期增加的資料量, 並保持在限制範圍內, 或當您想要限制工作區未規劃的費用時使用。  

若達到每日限制，將停止收集當天剩餘時間的需計費資料類型。 在所選取的 Log Analytics 工作區中，頁面頂端會出現警告橫幅，且系統會將作業事件傳送至 **LogManagement** 類別下的「Operation」 資料表。 一旦過了「每日限制的設定時間」下定義的重設時間後，資料收集就會繼續執行。 我們建議您根據此作業事件定義警示規則，設定為在達到每日資料限制時發出通知。 

> [!NOTE]
> 每日上限不會停止 Azure 資訊安全中心的資料收集。

### <a name="identify-what-daily-data-limit-to-define"></a>識別要定義的每日資料限制

檢閱 [Log Analytics 使用量和估計成本](usage-estimated-costs.md)，以了解資料擷取趨勢及要定義的每日資料量上限。 此謹慎考量，因為達到限制之後，您將無法監視您的資源。 

### <a name="manage-the-maximum-daily-data-volume"></a>管理每日資料量上限

下列步驟說明如何設定限制, 以管理 Log Analytics 工作區每天將內嵌的資料量。  

1. 在工作區中，從左側窗格中選取 [使用量和估計成本]。
2. 在所選工作區的 [使用量和估計成本] 頁面上，按一下頁面頂端的 [資料量管理]。 
3. 每日上限預設為 [關閉] – 請按一下 [開啟] 來加以啟用，然後設定資料量限制 (GB/天)。

    ![Log Analytics 設定資料限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>已達每日上限時發出警示

雖然我們會在您達到資料限制閾值時，於 Azure 入口網站中顯示視覺提示，但對於需要立刻處理的作業問題，此行為並不一定與您的管理方式一致。  若要接收警示通知，您可以在 Azure 監視器中建立新的警示規則。  若要深入瞭解, 請參閱[如何建立、查看和管理警示](alerts-metric.md)。

為協助您開始使用，以下是警示的建議設定：

- 目標:選取您的 Log Analytics 資源
- 準則： 
   - 訊號名稱：自訂記錄搜尋
   - 搜尋查詢：Operation | where Detail has 'OverQuota'
   - 依據：結果數目
   - 條件：大於
   - 閾值：0
   - 期間：5 (分鐘)
   - 頻率：5 (分鐘)
- 警示規則名稱：已達到每日資料限制
- 嚴重性：警告 (嚴重性 1)

一旦定義警示且達到限制後，警示就會觸發，並執行動作群組中定義的回應。 其可以透過電子郵件和文字簡訊通知您的小組，或使用 Webhook、自動化 Runbook 或[與外部 ITSM 方案整合](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)來自動採取動作。 

## <a name="change-the-data-retention-period"></a>變更資料保留期

下列步驟說明如何設定您工作區中的記錄資料保留時間。
 
1. 在工作區中，從左側窗格中選取 [使用量和估計成本]。
2. 在 [使用量和估計成本] 頁面上，按一下頁面頂端的 [資料量管理]。
3. 在窗格上，移動滑桿來增加或減少天數，然後按一下 [確定]。  如果您位於「免費」層，將無法修改資料保留期，必須升級至付費層，才能控制此設定。

    ![變更工作區資料保留設定](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
您也可以使用`dataRetention`參數, 透過 ARM 來[設定](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)保留期。 此外, 如果您將資料保留期設定為30天, 您可以使用`immediatePurgeDataOn30Days`參數觸發立即清除較舊的資料, 這可能適用于合規性相關案例。 此功能只會透過 ARM 公開。 

## <a name="legacy-pricing-tiers"></a>舊版定價層

在2018年4月2日之前擁有 Log Analytics 工作區或 Application Insights 資源的訂用帳戶, 或連結到在2019年2月1日之前啟動的 Enterprise 合約, 將可繼續擁有使用舊版定價層的存取權:**免費**、**獨立 (每 GB)** 和**每個節點 (OMS)** 。  免費定價層中的工作區會將每日資料內嵌限制為 500 MB (但 Azure 資訊安全中心所收集的安全性資料類型除外), 而且資料保留期僅限7天。 免費定價層僅供評估之用。 獨立或每個節點定價層中的工作區, 具有最多2年的使用者可設定保留期。 

在2016年4月之前建立的工作區也可以存取原始**標準**和**Premium**定價層, 分別保留30和365天的固定資料。 無法在**標準**或高階定價層中建立新的工作區, 而且如果工作區移出這些層級, 就無法將其移回。 

如需定價層限制的詳細資料, 請參閱[這裡](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)。

> [!NOTE]
> 若要使用來自購買 OMS E1套件、OMS E2 套件或 OMS Add-On for System Center 的權利，請選擇 Log Analytics [每個節點] 定價層。


## <a name="changing-pricing-tier"></a>正在變更定價層

如果您的 Log Analytics 工作區可以存取舊版的定價層，則可以在舊版的定價層之間進行變更：

1. 在 Azure 入口網站的 [Log Analytics 訂用帳戶] 窗格中，選取工作區。

2. 在 [工作區] 窗格的 [一般] 下方，選取 [定價層]。  

3. 在 [定價層] 下方選取定價層，然後按一下 [選取]。  
    ![選取的定價方案](media/manage-cost-storage/workspace-pricing-tier-info.png)

您也可以使用`ServiceTier`參數, 透過[ARM 設定定價層](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)。 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>針對 Log Analytics 為什麼不再收集資料的問題進行疑難排解

如果您處於舊版「免費」定價層並在某日傳送了超過 500 MB 的資料，就會停止收集當日的其餘資料。 達到每日限制是 Log Analytics 停止收集資料或資料似乎遺失的常見原因。  當資料收集開始及停止時，Log Analytics 會建立 Operation 類型的事件。 在搜尋中執行下列查詢，即可檢查您是否達到每日限制並遺失資料： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

當資料收集停止時, OperationStatus 為**Warning**。 當資料收集開始時, OperationStatus 就會**成功**。 下表描述資料收集停止的原因，並建議為繼續資料收集所要採取的動作：  

|收集停止的原因| 方案| 
|-----------------------|---------|
|已達舊版免費定價層的每日限制 |請等到隔天自動重新開始收集，或變更為付費定價層。|
|已達您工作區的每日上限|等到自動重新開始收集或提高每日資料量限制，如「管理每日資料量上限」中所述。 每日上限重設時間會顯示於 [資料量管理] 頁面上。 |
|Azure 訂用帳戶處於暫停狀態，原因如下：<br> 免費試用已結束<br> Azure Pass 已過期<br> 已達每月消費限制 (例如 MSDN 或 Visual Studio 訂閱)|轉換成付費訂閱<br> 移除限制，或等到限制重設|

若要在資料收集停止時收到通知, 請使用「*建立每日資料上限*」警示中所述的步驟, 以在資料收集停止時收到通知。 使用[建立動作群組](action-groups.md)中所述的步驟來設定警示規則的電子郵件、webhook 或 runbook 動作。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>針對使用量高於預期的原因進行疑難排解

較高的使用量是由下列一個或兩個原因所造成：
- 超過預期將資料傳送至 Log Analytics 工作區的節點數
- 傳送到 Log Analytics 工作區的資料超過預期

## <a name="understanding-nodes-sending-data"></a>瞭解傳送資料的節點

若要瞭解上個月每天報告的電腦數目, 請使用

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

若要取得將計費為節點的電腦清單 (如果工作區在舊版的每個節點定價層中), 請尋找傳送**計費資料類型**的節點 (某些資料類型是免費的)。 若要執行這項操作`_IsBillable` , 請使用[屬性](log-standard-properties.md#_isbillable), 並使用完整功能變數名稱的最左邊欄位。 這會傳回具有計費資料的電腦清單:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

看到的可計費節點計數可以預估為: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> 請謹慎使用這些 `union withsource = tt *` 查詢，因為執行跨資料類型掃描的費用相當高昂。 此查詢會取代使用 Usage 資料類型查詢每一電腦資訊的舊方式。  

實際計費的確切計算方式是取得每小時傳送計費資料類型的電腦計數。 (針對舊版每個節點定價層中的工作區, Log Analytics 會計算需要按小時計費的節點數目)。 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>瞭解內嵌資料量

在 [使用量和估計成本] 頁面上，[每個解決方案的資料擷取] 圖表會顯示所傳送的資料總量，以及每個解決方案所傳送的資料量。 這可讓您判斷各種趨勢，例如整體資料使用量 (或特定解決方案的使用量) 是否正在增長、穩定不變或正在減少。 用來產生此內容的查詢為：

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

請注意，子句 "where IsBillable = true" 會篩選掉來自無擷取成本之特定解決方案的資料類型。 

您可以進一步向下鑽研，以查看特定資料類型的資料趨勢，例如假設您想研究基於 IIS 記錄的資料：

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>資料量 (依電腦)

若要查看每部電腦內嵌的可計費事件**大小**, 請`_BilledSize`使用[屬性](log-standard-properties.md#_billedsize), 它會提供以位元組為單位的大小:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

[屬性](log-standard-properties.md#_isbillable)會指定內嵌資料是否會產生費用。`_IsBillable`

若要查看每部電腦內嵌的可**計費**事件計數, 請使用 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

如果您想要查看有哪些可計費資料類型的計數正在傳送資料到特定的電腦，請使用：

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>依 Azure 資源、資源群組或訂用帳戶的資料量

針對 Azure 中裝載之節點的資料, 您可以取得每一部__電腦__內嵌的可計費事件**大小**, 使用 _ResourceId[屬性](log-standard-properties.md#_resourceid), 它會提供資源的完整路徑:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

針對 azure 中裝載之節點的資料, 您可以取得__每個 azure 訂__用帳戶所內嵌的可`_ResourceId`計費事件**大小**, 並將屬性剖析為:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

將`subscriptionId`變更`resourceGroup`為會顯示 Azure 資源群組的可計費內嵌資料量。 


> [!NOTE]
> [使用量] 資料類型的部分欄位雖然仍位於結構描述中，但皆已過時，且系統將不再填入其值。 這包括 [Computer]，以及其他與擷取相關的欄位 ([TotalBatches]、[BatchesWithinSla]、[BatchesOutsideSla]、[BatchesCapped]，以及 [AverageProcessingTimeMs])。

### <a name="querying-for-common-data-types"></a>查詢常見的資料類型

若要更深入挖掘特定資料類型的資料來源，以下是一些實用的範例查詢：

+ **安全性**解決方案
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **記錄管理**解決方案
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** 資料類型
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** 資料類型
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 資料類型
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 資料類型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>降低成本的訣竅

降低所收集記錄量的一些建議包括：

| 高資料量的來源 | 如何縮減資料量 |
| -------------------------- | ------------------------- |
| 安全性事件            | 選取[一般或最小安全性事件](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> 變更安全性稽核原則為只收集所需事件。 特別檢閱下列原則是否需要收集事件： <br> - [a稽核篩選平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [稽核登錄](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [稽核檔案系統](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [稽核核心物件](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [稽核控制代碼操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 稽核抽取式存放裝置 |
| 效能計數器       | 變更[效能計數器組態](data-sources-performance-counters.md)以： <br> - 減少收集頻率 <br> - 減少效能計數器的數目 |
| 事件記錄                 | 變更[事件記錄組態](data-sources-windows-events.md)以： <br> - 減少所收集的事件記錄數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」層級事件 |
| Syslog                     | 變更 [Syslog 組態](data-sources-syslog.md)以： <br> - 減少所收集的設施數目 <br> - 只收集必要的事件層級。 例如，不要收集「資訊」和「偵錯」層級事件 |
| AzureDiagnostics           | 變更資源記錄集合： <br> - 減少會將記錄傳送至 Log Analytics 的資源數目 <br> - 只收集必要的記錄 |
| 電腦中不需要解決方案的方案資料 | 使用[方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。 |

### <a name="getting-security-and-automation-node-counts"></a>取得安全性和自動化節點計數

如果您是處於「每節點 (OMS)」定價層，則系統會根據您使用的節點和解決方案數目來向您收取費用，您需支付費用的見解與分析節點數目將會顯示在 [使用量和估計成本] 頁面上的資料表中。  

若要查看不同安全性節點的數目，您可以使用此查詢：

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

若要查看不同自動化節點的數目，請使用此查詢：

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>在資料收集偏高時建立警示

本節說明在下列情況下如何建立警示：
- 資料量超出指定的數量。
- 資料量預計會超出指定的數量。

Azure 警示支援使用搜尋查詢的[記錄警示](alerts-unified-log.md)。 

在過去 24 小時收集超過 100GB 的資料時，下列查詢的結果：

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

下列查詢會使用簡單的公式來預測在一天中何時會傳送超過 100GB 的資料： 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

若要針對不同的資料量顯示警示，請將查詢中的 100 變更為您要顯示警示的 GB 數。

使用[建立新記錄警示](alerts-metric.md)中所述的步驟，可在資料收集高於預期時收到通知。

建立第一個查詢的警示時 - 在 24 小時內有超過 100GB 的資料時，請：  

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **訊號名稱**：選取 [自訂記錄搜尋]
   - 將 [搜尋查詢] 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - [警示邏輯] 為 [根據結果數目]，而 [條件] 為 [大於臨界值 0]
   - [時間週期] 為 1440 分鐘，而 [警示頻率] 設定為 60 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱] 設定為「在 24 小時內大於 100GB 的資料量」
   - 將 [嚴重性] 設定為「警告」

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

建立第二個查詢的警示時 - 預計在 24 小時內會有超過 100GB 的資料時，請：

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則**：指定下列項目：
   - **訊號名稱**：選取 [自訂記錄搜尋]
   - 將 [搜尋查詢] 設定為 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - [警示邏輯] 為 [根據結果數目]，而 [條件] 為 [大於臨界值 0]
   - [時間週期] 為 180 分鐘，而 [警示頻率] 設定為 60 分鐘，因為使用量資料每小時只會更新一次。
- **定義警示詳細資料**：指定下列項目：
   - 將 [名稱] 設定為「預計在 24 小時內大於 100GB 的資料量」
   - 將 [嚴重性] 設定為「警告」

指定現有或建立新的[動作群組](action-groups.md)，以便在記錄警示符合準則時收到通知。

當您收到警示時，請使用下一節中的步驟，針對使用量高於預期的原因進行疑難排解。

## <a name="limits-summary"></a>限制摘要

還有一些額外的 Log Analytics 限制, 其中有些會相依于 Log Analytics 定價層。 這些內容記載在[這裡](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)。


## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-query/log-query-overview.md), 以瞭解如何使用搜尋語言。 您可以使用搜尋查詢，對使用量資料執行額外的分析。
- 使用[建立新的記錄警示](alerts-metric.md)中所述的步驟，可在符合搜尋條件時收到通知。
- 使用[方案目標](../insights/solution-targeting.md)，只從必要的電腦群組收集資料。
- 若要設定有效的事件收集原則，請檢閱 [Azure 資訊安全中心篩選原則](../../security-center/security-center-enable-data-collection.md)。
- 變更[效能計數器組態](data-sources-performance-counters.md)。
- 若要修改事件收集設定，請檢閱[事件記錄組態](data-sources-windows-events.md)。
- 若要修改 syslog 收集設定，請檢閱 [Syslog 組態](data-sources-syslog.md)。