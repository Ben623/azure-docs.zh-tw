---
title: 管理能力和監視-查詢活動、資源使用率
description: 了解哪些功能可用來管理和監視 Azure SQL 資料倉儲。 使用 Azure 入口網站和動態管理檢視 (DMV) 來了解資料倉儲的查詢活動和資源使用率。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a210e2622212ed59dfa12f9f9a108c6ffe08714
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692890"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>監視 Azure SQL 資料倉儲中的資源使用率和查詢活動
Azure SQL 資料倉儲在 Azure 入口網站中提供豐富的監視體驗，讓您可看到資料倉儲工作負載的深入解析。 Azure 入口網站是監視資料倉儲的建議工具，因為其提供可設定的保留期、警示、建議，以及可自訂的計量與記錄圖表及儀表板。 入口網站也可讓您與其他 Azure 監視服務（例如 Operations Management Suite （OMS）和 Azure 監視器（記錄））整合，以同時針對您的資料倉儲，以及整個 Azure 分析提供全面的監視體驗。整合式監視體驗的平臺。 本文件將說明哪些監視功能可用來最佳化及管理分析平台與 SQL 資料倉儲。 

## <a name="resource-utilization"></a>資源使用率 
在 Azure 入口網站中，下列計量可供 SQL 資料倉儲使用。 我們透過 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)提供這些計量。


| 度量名稱             | 說明                                                  | 彙總類型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 資料倉儲上所有節點的 CPU 使用率      | 最大值          |
| 資料 IO 百分比      | 資料倉儲上所有節點的 IO 使用率       | 最大值          |
| 記憶體百分比       | 資料倉儲所有節點的記憶體使用率（SQL Server） | 最大值          |
| 成功的連線  | 資料的成功連數目                 | 總計            |
| 失敗的連線      | 資料倉儲的失敗連線數量           | 總計            |
| 遭到防火牆封鎖     | 資料倉儲的已封鎖登入數目     | 總計            |
| DWU 限制               | 資料倉儲的服務等級目標                | 最大值          |
| DWU 百分比          | CPU 百分比與資料 IO 百分比之間的最大值        | 最大值          |
| 已使用 DWU                | DWU 限制 * DWU 百分比                                   | 最大值          |
| 快取命中的百分比    | (快取命中數 / 快取遺漏) * 100  其中快取命中數是在本機 SSD 快取中所有資料行存放區的區段命中數總和，而快取遺漏是本機 SSD 快取中資料行存放區的區段遺漏數 (從所有節點上加總) | 最大值          |
| 已用快取的百分比   | (已用快取 / 快取容量) * 100 其中已用快取是本機 SSD 快取 (跨所有節點) 中的所有位元組總和，而快取容量是本機 SSD 快取 (跨所有節點) 的儲存體容量總和 | 最大值          |
| 本機 tempdb 百分比 | 跨所有計算節點的本機 tempdb 使用率 - 此值每五分鐘發出一次 | 最大值          |

> 在查看計量和設定警示時所要考慮的事項：
>
> - 系統會針對特定資料倉儲回報失敗和成功的連線，而不是邏輯伺服器的連接
> - 即使資料倉儲處於閒置狀態，記憶體百分比也會反映使用率，而不會反映作用中的工作負載記憶體耗用量。 使用並追蹤此計量，以及其他人（tempdb、gen2 快取），以在調整額外的快取容量時，全面決定是否會增加工作負載效能以符合您的需求。


## <a name="query-activity"></a>查詢活動
為了在透過 T-SQL 監視 SQL 資料倉儲時有程式設計體驗，此服務提供一組動態管理檢視 (DMV)。 若要對您的工作負載進行主動式疑難排解和識別效能瓶頸時，這些檢視十分實用。

若要檢視 SQL 資料倉儲提供的 DMV 清單，請參閱此[文件](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>計量和診斷記錄
計量和記錄都可以匯出至 Azure 監視器，特別是[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)檔元件，而且可以透過[記錄查詢](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)以程式設計方式存取。 SQL 資料倉儲的記錄延遲大約是10-15 分鐘。 如需影響延遲的因素的詳細資訊，請造訪下列檔。


## <a name="next-steps"></a>後續步驟
下列使用說明指南會說明監視和管理資料倉儲時的常見案例及使用案例：

- [使用 DMV 監視您的資料倉儲工作負載](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
