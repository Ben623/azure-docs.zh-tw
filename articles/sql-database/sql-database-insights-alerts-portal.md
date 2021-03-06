---
title: 設定警示和通知（Azure 入口網站）
description: 使用 Azure 入口網站建立 SQL Database 的警示，在符合指定條件時觸發通知或自動化。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209533"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>使用 Azure 入口網站建立 Azure SQL Database 和 Azure Synapse 分析資料庫的警示

## <a name="overview"></a>概觀

本文說明如何使用 Azure 入口網站，在 Azure SQL Database 和 Azure Synapse Analytics （先前稱為 Azure SQL 資料倉儲）中設定單一、集區和資料倉儲資料庫的警示。 當某些計量 (例如，資料庫大小或 CPU 使用量) 閾值時，警示可傳送電子郵件給您或呼叫 Webhook。 本文也提供設定警示期間的最佳做法。

> [!IMPORTANT]
> 此功能還無法在受控執行個體中取得。 或者，您可以根據[動態管理檢視](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) \(機器翻譯\)，針對某些計量使用 SQL 代理程式傳送電子郵件警示。

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

* **計量值** - 當指定的計量值超出您在任一方向指派的臨界值時會觸發警示。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。
* **活動記錄檔事件** - 警示可觸發*每一個*事件，或是僅在發生特定事件數目時才觸發。

您可以在警示觸發時，設定警示執行下列動作︰

* 傳送電子郵件通知給服務管理員和共同管理員
* 傳送電子郵件給您指定的其他電子郵件。
* 呼叫 Webhook

您可以透過下列方式設定和取得有關警示規則的資訊

* [Azure 入口網站](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [命令列介面 (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 入口網站建立計量的警示規則

1. 在 [入口網站](https://portal.azure.com/)中，找到您要監視的資源並選取。
2. 在 [監視] 區段中選取 [**警示**]。 不同資源的文字和圖示會有些許不同。  

   ![監視](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. 選取 [**新增警示規則**] 按鈕，以開啟 [**建立規則**] 頁面。
  ![建立規則](media/sql-database-insights-alerts-portal/create-rule.png)

4. 在 [**條件**] 區段中，按一下 [**新增**]。
  ![定義條件](media/sql-database-insights-alerts-portal/create-rule.png)
5. 在 [**設定信號邏輯**] 頁面中，選取信號。
  ![選取 [信號]](media/sql-database-insights-alerts-portal/select-signal.png)。
6. 選取信號（例如**CPU 百分比**）之後，[**設定信號邏輯**] 頁面隨即出現。
  ![設定信號邏輯](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. 在此頁面上，設定該閾數值型別、運算子、匯總類型、臨界值、匯總資料細微性和評估頻率。 然後按一下 [完成]。
8. 在 [**建立規則**] 中，選取現有的**動作群組**或建立新的群組。 動作群組可讓您定義要在警示條件發生時採取的動作。
  ![定義動作群組](media/sql-database-insights-alerts-portal/action-group.png)

9. 定義規則的 [名稱]、提供選擇性的 [描述]、選擇規則的嚴重性層級、選擇是否要在建立規則時啟用規則，然後按一下 [**建立規則警示**] 以建立度量規則警示。

在10分鐘內，警示會處於作用中狀態，如先前所述的觸發程式。

## <a name="next-steps"></a>後續步驟

* 深入了解 [在警示中設定 webhook](../azure-monitor/platform/alerts-webhooks.md)。
