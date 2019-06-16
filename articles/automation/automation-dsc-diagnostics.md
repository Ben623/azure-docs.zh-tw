---
title: 将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志
description: 本文演示如何将 Desired State Configuration (DSC) 报表数据从 Azure Automation State Configuration 发送到 Azure Monitor 日志，以便为用户提供附加见解和管理信息。
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 50779f8a37713bda8b27c1cfd2ca37eed4edbd11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054706"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志

Azure 自動化狀態設定將保留 30 天的節點狀態資料。
如果您想要保留這項資料更長的時間，您可以將節點狀態資料傳送給您的 Log Analytics 工作區中。
節點以及節點組態中個別 DSC 資源的合規性狀態會顯示在 Azure 入口網站中或使用 PowerShell 顯示。
可以使用 Azure Monitor 日志进行以下操作：

- 取得受控節點與個別資源的合規性資訊
- 根據合規性狀態觸發電子郵件或警示
- 撰寫受控節點之間的進階查詢
- 相互關聯自動化帳戶之間的合規性狀態
- 以視覺化方式呈現一段時間的合規性歷程記錄

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必要條件

若要开始将 Automation State Configuration 报表发送到 Azure Monitor 日志，需要准备：

- 2016 年 11 月或更新版本的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
- Azure 自動化帳戶。 如需詳細資訊，請參閱[開始使用 Azure 自動化](automation-offering-get-started.md)。
- 提供 [自動化與控制]  服務供應項目的 Log Analytics 工作區。 有关详细信息，请参阅 [Azure Monitor 日志入门](../log-analytics/log-analytics-get-started.md)。
- 至少一個 Azure Automation State Configuration 節點。 如需詳細資訊，請參閱[將機器上架交由 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>设置与 Azure Monitor 日志的集成

若要开始将数据从 Azure Automation DSC 导入到 Azure Monitor 日志，请完成以下步骤：

1. 在 PowerShell 中登入您的 Azure 帳戶。 請參閱[使用 Azure PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. 執行下列 PowerShell 命令以取得自動化帳戶的 _ResourceId_：(如有多個自動化帳戶，請選擇您想要設定的帳戶 _ResourceID_)。

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 執行下列 PowerShell 命令以取得 Log Analytics 工作區的 _ResourceId_：(如有多個工作區，請選擇您想要設定的工作區 _ResourceID_)。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 執行下列 PowerShell 命令，以前述各步驟的 _ResourceId_ 值取代 `<AutomationResourceId>` 和 `<WorkspaceResourceId>`：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

若要停止将数据从 Azure Automation State Configuration 导入到 Azure Monitor 日志，请运行以下 PowerShell 命令：

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>檢視 State Configuration 記錄

为 Automation State Configuration 数据设置与 Azure Monitor 日志的集成后，“日志搜索”  按钮会出现在自动化帐户的“DSC 节点”  边栏选项卡上。 按一下 [記錄搜尋]  按鈕以檢視 DSC 節點資料的記錄。

![記錄搜尋按鈕](media/automation-dsc-diagnostics/log-search-button.png)

[記錄搜尋]  刀鋒視窗隨即開啟，而且您會在套用到該節點的節點組態中，看到每個 State Configuration 節點呼叫 **DscNodeStatusData** 作業，和每個 [DSC 資源](/powershell/dsc/resources)呼叫 **DscResourceStatusData** 作業。

**DscResourceStatusData** 作業包含所有失敗 DSC 資源的錯誤資訊。

按一下清單中的每項作業可查看該作業的資料。

还可以通过在 Azure Monitor 日志中进行搜索来查看日志。
請參閱[使用記錄搜尋尋找資料](../log-analytics/log-analytics-log-searches.md)。
鍵入下列查詢來尋找 State Configuration 記錄：`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

您也可以依作業名稱縮小查詢範圍。 例如：`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 合規性檢查失敗時傳送電子郵件

我們最常從客戶收到的其中一個問題，便是希望系統能在 DSC 設定發生問題時，傳送電子郵件或簡訊通知他們。

若要建立警示規則，首先針對應叫用警示的 State Configuration 報告記錄，建立記錄檔搜尋。 按一下 [+ 新增警示規則]  按鈕，以建立並設定警示規則。

1. 在“Log Analytics 工作区概述”页中，单击“日志”  。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`。

   如果您已將來自多個自動化帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或自動化帳戶來將警示分組。  
   自動化帳戶名稱可以衍生自 DscNodeStatusData 搜尋的 [資源] 欄位。  
1. 若要開啟 [建立規則]  畫面，按一下頁面頂端的 [+ 新增警示規則]  。 有关警报配置选项的详细信息，请参阅[创建警报规则](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有節點間尋找失敗的 DSC 資源

使用 Azure Monitor 日志的一个优点是，可以在节点中搜索失败的检查。
尋找所有失敗的 DSC 資源執行個體。

1. 在“Log Analytics 工作区概述”页中，单击“日志”  。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`。

### <a name="view-historical-dsc-node-status"></a>檢視歷程記錄 DSC 節點狀態

最後，您也許想要以視覺化方式呈現一段時間的 DSC 節點狀態歷程記錄。  
您可以使用此查詢來搜尋 DSC 節點狀態一段時間後的狀態。

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

這會顯示一段時間的節點狀態圖表。

## <a name="azure-monitor-logs-records"></a>Azure Monitor 日志记录

来自 Azure 自动化的诊断将在 Azure Monitor 日志中创建两种类别的记录。

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |DscNodeStatusData |
| ResultType |節點是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| NodeComplianceStatus_s |節點是否符合規範。 |
| DscReportStatus |合規性檢查是否已順利執行。 |
| ConfigurationMode | 設定如何套用至節點。 可能的值為 __"ApplyOnly"__ 、 __"ApplyandMonitior"__ 和 __"ApplyandAutoCorrect"__ 。 <ul><li>__ApplyOnly__：DSC 會套用設定但不執行任何進一步的動作，除非有新的設定發送到目標節點，或從伺服器提取新的設定時。 初始套用新的設定之後，DSC 不會檢查先前設定的狀態是否漂移。 DSC 在 __ApplyOnly__ 生效之前會一直嘗試套用設定，直到成功為止。 </li><li> __ApplyAndMonitor__：這是預設值。 LCM 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異。 DSC 在 __ApplyAndMonitor__ 生效之前會一直嘗試套用設定，直到成功為止。</li><li>__ApplyAndAutoCorrect__：DSC 會套用任何新的組態。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異，然後重新套用目前的設定。</li></ul> |
| HostName_s | 受控節點名稱。 |
| IPAddress | 受控節點的 IPv4 位址。 |
| Category | DscNodeStatus |
| Resource | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| NodeId_g |識別受控節點的 GUID。 |
| DscReportId_g |識別報表的 GUID。 |
| LastSeenTime_t |上一次檢視報表的日期和時間。 |
| ReportStartTime_t |報表開始的日期和時間。 |
| ReportEndTime_t |報表完成的日期和時間。 |
| NumberOfResources_d |在節點套用的設定中呼叫的 DSC 資源數目。 |
| SourceSystem | Azure Monitor 日志收集数据的方式。 針對 Azure 診斷，一律為 Azure  。 |
| ResourceId |指定 Azure 自動化帳戶。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |為更新狀態報告之相互關聯識別碼的 GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |DscResourceStatusData|
| ResultType |資源是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| Category | DscNodeStatus |
| Resource | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| NodeId_g |識別受控節點的 GUID。 |
| DscReportId_g |識別報表的 GUID。 |
| DscResourceId_s |DSC 資源執行個體的名稱。 |
| DscResourceName_s |DSC 資源的名稱。 |
| DscResourceStatus_s |DSC 資源是否符合規範。 |
| DscModuleName_s |包含 DSC 資源的 PowerShell 模組名稱。 |
| DscModuleVersion_s |包含 DSC 資源的 PowerShell 模組版本。 |
| DscConfigurationName_s |節點套用的設定名稱。 |
| ErrorCode_s | 資源失敗時的錯誤代碼。 |
| ErrorMessage_s |資源失敗時的錯誤訊息。 |
| DscResourceDuration_d |DSC 資源執行的時間，以秒為單位。 |
| SourceSystem | Azure Monitor 日志收集数据的方式。 針對 Azure 診斷，一律為 Azure  。 |
| ResourceId |指定 Azure 自動化帳戶。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |為更新狀態報告之相互關聯識別碼的 GUID。 |

## <a name="summary"></a>總結

将 Automation State Configuration 数据发送到 Azure Monitor 日志后，可以通过以下操作更好地了解 Automation State Configuration 节点的状态：

- 設定警示，在發生問題時通知您
- 使用自訂檢視和搜尋查詢，以視覺化方式檢視您的 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。  

Azure Monitor 日志可以更直观地显示 Automation State Configuration 数据的运行情况，并且有助于更快地解决事件。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure 自動化狀態設定](automation-dsc-overview.md)
- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)
- 若要了解如何編譯 DSC 組態，以將它們指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的組態](automation-dsc-compile.md)
- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
- 若要深入了解如何建構不同的搜尋查詢，以及檢閱使用 Azure 監視器記錄檔的自動化狀態設定記錄檔，請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)
- 若要深入了解 Azure 監視器記錄檔和資料收集來源，請參閱[Azure 監視器中收集 Azure 儲存體的資料記錄檔概觀](../azure-monitor/platform/collect-azure-metrics-logs.md)
