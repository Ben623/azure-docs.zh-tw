---
title: 將 Azure 資訊安全中心警示和建議匯出至 Siem |Microsoft Docs
description: 本文說明如何設定連續匯出安全性警示和建議至 Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cd26ed446ce676bcec85d8e413d3ec37ac236869
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521991"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>匯出安全性警示和建議（預覽）

Azure 資訊安全中心會產生詳細的安全性警示和建議。 您可以在入口網站中或透過程式設計工具來觀看這些程式。 您可能也需要匯出這項資訊，或將它傳送至環境中的其他監視工具。 

本文說明（預覽）工具組，可讓您以手動或持續的方式來匯出警示和建議。

使用這些工具，您可以：

* 以 CSV 的形式產生詳細報告
* 匯出至 Log Analytics 工作區
* 匯出至 Azure 事件中樞（以與協力廠商 Siem 整合）

## <a name="setting-up-a-continuous-export"></a>設定連續匯出

1. 從資訊安全中心的提要欄位中，按一下 **定價 & 設定**。

1. 選取您要設定資料匯出的特定訂用帳戶。
    
1. 從該訂用帳戶的 [設定] 頁面的提要欄位中，選取 [**連續匯出（預覽）** ]。

    [![Azure 資訊安全中心中的匯出選項](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox)您會在這裡看到 [匯出] 選項。 每個可用的匯出目標都有一個索引標籤。 

1. 選取您想要匯出的資料類型，並從每個類型的篩選中選擇（例如，[僅匯出高嚴重性警示]）。

1. 從 [匯出目標] 區域中，選擇您想要儲存資料的位置。 資料可以儲存在不同訂用帳戶上的目標中（例如，在中央事件中樞實例或中央 Log Analytics 工作區）。

1. 按一下 [儲存]。

## <a name="continuous-export-through-azure-event-hubs"></a>透過 Azure 事件中樞連續匯出  

> [!NOTE]
> 在大部分情況下，將監視資料串流至外部工具的最有效方法是使用 Azure 事件中樞。 [本文](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)提供如何將來自不同來源的監視資料串流至事件中樞的簡短描述，以及詳細指引的連結。

> [!NOTE]
> 如果您先前已使用 Azure 活動記錄將資訊安全中心警示匯出至 SIEM，下列程式會取代該方法。

### <a name="to-integrate-with-a-siem"></a>與 SIEM 整合 

在您將所選資訊安全中心資料的連續匯出設定為 [Azure 事件中樞] 之後，您可以依照下列指示，在您的 SIEM 上設定適當的連接器。

請遵循[此頁面](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable)與您 SIEM 相關的指示，並使用相關的連接器：

* **Splunk** -使用[適用于 Splunk 的 Azure 監視器附加](https://splunkbase.splunk.com/app/3534/)元件
* **IBM QRadar** -使用[手動設定的記錄來源](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** –使用[SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

如果您使用**Azure Sentinel**，請使用此處提供的原生 Azure 資訊安全中心警示[資料連線器](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)。

此外，如果您想要從已設定的事件中樞自動將連續匯出的資料移至 Azure 資料總管，請使用將[資料從事件中樞內嵌至 azure 資料總管](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)中的指示。


## <a name="continuous-export-to-log-analytics-workspace"></a>連續匯出至 Log Analytics 工作區

若要匯出至 Log Analytics 工作區，您必須在工作區上啟用資訊安全中心的免費或標準層 Log Analytics 解決方案。 如果您使用 Azure 入口網站，則當您啟用連續匯出時，資訊安全中心免費層解決方案會自動啟用。 不過，如果您要以程式設計方式設定連續匯出設定，您必須從**定價 & 設定**中，手動選取所需工作區的免費或標準定價層。  

安全性警示和建議會分別儲存在*SecurityAlert*和*SecurityRecommendations*資料表中。 包含這些資料表的 Log Analytics 解決方案名稱，取決於您是在免費或標準層（請參閱[定價](security-center-pricing.md)）：安全性或 SecurityCenterFree。

![Log Analytics 中的 * SecurityAlert * 資料表](./media/continuous-export/log-analytics-securityalert-solution.png)

## <a name="manual-one-time-export-of-security-alerts"></a>手動一次匯出安全性警示

若要下載警示或建議的 CSV 報告，請開啟 [**安全性警示**或**建議**] 頁面，然後按一下 [**下載 CSV 報表（預覽）** ] 按鈕。

[![將警示資料下載為 CSV 檔案](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 這些報告包含 Azure 入口網站中 [目錄 + 訂用帳戶] 篩選中目前所選取訂用帳戶之資源的警示和建議： ![選取 [目錄 + 訂用帳戶] 的篩選](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何設定您的建議和警示的連續匯出。 您也已瞭解如何將警示資料下載為 CSV 檔案。 

如需相關內容，請參閱下列檔： 

- [Azure 事件中樞檔](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel 檔](https://docs.microsoft.com/azure/sentinel/)
- [Azure 監視器文件](https://docs.microsoft.com/azure/azure-monitor/)