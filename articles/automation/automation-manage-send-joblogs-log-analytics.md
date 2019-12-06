---
title: 將 Azure 自動化作業資料轉送至 Azure 監視器記錄
description: 本文示範如何將作業狀態和 runbook 作業串流傳送至 Azure 監視器記錄，以提供額外的深入解析和管理。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2433e8193026b8aaa3cbf29eb1411c7449a4953
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849729"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>將作業狀態和作業串流從自動化轉送至 Azure 監視器記錄

「自動化」可以將 Runebook 工作狀態和工作資料流傳送到您的 Log Analytics 工作區。 此程序不會涉及工作區連結且完全獨立。 作業記錄和作業串流會顯示於 Azure 入口網站中，或是使用 PowerShell，針對個別作業，而這可讓您執行簡單的調查。 現在有了 Azure 監視器的記錄，您可以：

* 取得自動化作業的相關見解。
* 根據您的 Runbook 作業狀態 (例如失敗或已暫止) 觸發電子郵件或警示。
* 撰寫跨多個作業串流的進階查詢。
* 使自動化帳戶之間的作業相互關聯。
* 將一段時間的作業記錄視覺化。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>先決條件和部署考量

若要開始將自動化記錄傳送到 Azure 監視器記錄檔，您需要：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)的最新版本。
* Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Azure 監視器記錄](../log-analytics/log-analytics-get-started.md)。
* 您「Azure 自動化」帳戶的 ResourceId。

尋找您「Azure 自動化」帳戶的 ResourceId：

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

若要尋找您 Log Analytics 工作區的 ResourceId，請執行下列 PowerShell：

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

如果您有多個「自動化」帳戶或工作區，請在先前命令的輸出中，尋找您需要設定的 *Name*，並複製 *ResourceId* 的值。

如果您需要尋找自動化帳戶的 Name，可在 Azure 入口網站中，從 [自動化帳戶] 刀鋒視窗選取您的自動化帳戶，然後選取 [所有設定]。 在 [所有設定] 刀鋒視窗中，選取 [帳戶設定] 之下的 [屬性]。  在 [屬性] 刀鋒視窗中，您可以記下這些值。<br> ![自動化帳戶屬性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器記錄的整合

1. 在您的電腦上，從 [開始] 畫面啟動 **Windows PowerShell**。
2. 執行下列 PowerShell，然後使用先前步驟的值來編輯 `[your resource id]` 和 `[resource id of the log analytics workspace]` 的值。

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

執行此腳本之後，可能需要一小時的時間，才能開始查看所寫入之新 JobLogs 或 JobStreams Azure 監視器記錄中的記錄。

若要查看記錄，請在 log analytics 記錄搜尋中執行下列查詢： `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>驗證組態

若要確認您的「自動化」帳戶會將記錄傳送到 Log Analytics 工作區，請使用下列 PowerShell 來確認已在「自動化」帳戶上正確設定診斷：

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

在輸出中，確定：

* 在 *Logs* 下方，*Enabled* 的值為 *True*。
* *WorkspaceId* 的值已設為 Log Analytics 工作區的 ResourceId。

## <a name="azure-monitor-log-records"></a>Azure 監視器記錄

來自 Azure 自動化的診斷會在 Azure 監視器記錄檔中建立兩種類型的記錄，並將其標記為**AzureDiagnostics**。 下列查詢會使用已升級的查詢語言來 Azure 監視器記錄。 如需舊版查詢語言與新的 Azure Kusto 查詢語言之間常見查詢的詳細資訊，請流覽[舊版 Azure Kusto 查詢語言](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)功能提要

### <a name="job-logs"></a>作業記錄

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| JobId_g |Runbook 作業之識別碼的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- New (新增)<br>-已建立<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Completed (已完成) |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobLogs。 |
| OperationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱 |
| SourceSystem | Azure 監視器記錄檔收集資料的方式。 針對 Azure 診斷，一律為 Azure 。 |
| ResultDescription |說明 Runbook 作業的結果狀態。 可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed |
| CorrelationId |Runbook 作業之相互關聯識別碼的 GUID。 |
| ResourceId |指定 Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>作業串流
| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的人員。 可能的值為電子郵件地址或排程作業的系統。 |
| StreamType_s |作業串流的類型。 可能的值包括：<br>-Progress (進度)<br>- Output (輸出)<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| JobId_g |Runbook 作業之識別碼的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- In Progress |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobStreams。 |
| OperationName | 指定在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱 |
| SourceSystem | Azure 監視器記錄檔收集資料的方式。 針對 Azure 診斷，一律為 Azure 。 |
| ResultDescription |包含來自 Runbook 的輸出串流。 |
| CorrelationId |Runbook 作業之相互關聯識別碼的 GUID。 |
| ResourceId |指定 Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>在 Azure 監視器記錄中查看自動化記錄

既然您已開始將自動化作業記錄傳送到 Azure 監視器記錄，讓我們來瞭解如何在 Azure 監視器記錄檔中使用這些記錄。

若要查看記錄，請執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>在 Runbook 工作失敗或暫停時傳送電子郵件
客戶最常要求的其中一項功能便是希望系統能在 Runbook 作業發生問題時，傳送電子郵件或文字通知。

若要建立警示規則，首先針對應叫用警示的 Runbook 工作記錄，建立記錄檔搜尋。 按一下 [警示] 按鈕，以建立並設定警示規則。

1. 從 Log Analytics 工作區的 [總覽] 頁面，按一下 [**查看記錄**]。
2. 在查詢欄位中輸入下列搜尋，來建立警示的記錄搜尋查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`  您也可以使用下列方式來依 RunbookName 分組：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   如果您將來自多個「自動化」帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或「自動化」帳戶來將警示分組。 您可以在搜尋 JobLogs 時，在 [資源] 欄位中找到「自動化」帳戶名稱。
3. 若要開啟 [建立規則] 畫面，按一下頁面頂端的 [+ 新增警示規則]。 如需設定警示選項的詳細資訊，請參閱 [Azure 中的記錄警示](../azure-monitor/platform/alerts-unified-log.md)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>尋找所有已完成但發生錯誤的工作
除了失敗的警示，您還可以尋找 Runbook 作業發生非終止錯誤的時間。 在這些情況下，PowerShell 會產生錯誤串流，但非終止錯誤不會造成您的作業暫止或失敗。

1. 在您的 Log Analytics 工作區中，按一下 [**記錄**]。
2. 在查詢欄位中，輸入 `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`，然後按一下 [搜尋] 按鈕。

### <a name="view-job-streams-for-a-job"></a>檢視工作的工作資料流
當您在針對作業進行偵錯時，也可以深入查看作業串流。 下列查詢會顯示單一作業具有 GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的所有資料流：

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>檢視歷史工作狀態
最後，您可能會想要視覺化一段時間的工作歷程記錄。 您可以使用此查詢來搜尋您的工作在一段時間的狀態。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 歷史工作狀態圖表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>移除診斷設定

若要從自動化帳戶中移除診斷設定，請執行下列命令：

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>總結

藉由將您的自動化作業狀態和串流資料傳送至 Azure 監視器記錄，您可以藉由下列方式取得自動化作業狀態的更深入解析：
+ 設定警示以在發生問題時通知您。
+ 使用自訂檢視和搜尋查詢，以視覺化方式檢視您的 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。

Azure 監視器記錄可為您的自動化作業提供更高的操作可見度，並有助於更快處理事件。

## <a name="next-steps"></a>後續步驟

* 如需針對 Log Analytics 進行疑難排解的說明，請參閱[疑難排解 Log analytics 不再收集資料的原因](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)。
* 若要深入瞭解如何使用 Azure 監視器記錄來建立不同的搜尋查詢，並查看自動化作業記錄，請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
* 若要了解如何從 Runbook 建立及擷取輸出與錯誤訊息，請參閱 [Runbook 輸出與訊息](automation-runbook-output-and-messages.md)。
* 若要深入了解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱[追蹤 Runbook 作業](automation-runbook-execution.md)。
* 若要深入瞭解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料總覽](../azure-monitor/platform/collect-azure-metrics-logs.md)。

