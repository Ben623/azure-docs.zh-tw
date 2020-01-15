---
title: 工作負載管理
description: 在 Azure SQL 資料倉儲中執行工作負載管理的指導方針。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 66edde9591d4491fa630772f99372f9901319b2f
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940588"
---
# <a name="what-is-workload-management"></a>什麼是工作負載管理？

執行混合工作負載可能會造成忙碌系統上的資源挑戰。  解決方案架構設計人員會尋求各種方式來分隔傳統資料倉儲活動（例如載入、轉換和查詢資料），以確保有足夠的資源存在，以達到 Sla。  

實體伺服器隔離可能會導致隨身使用量過低、overbooked 的基礎結構，或處於使用硬體啟動和停止的快取持續可能遭受的狀態。  成功的工作負載管理配置可有效地管理資源、確保高效率的資源使用率，以及最大化的投資報酬率（ROI）。

資料倉儲工作負載指的是與資料倉儲相關的所有作業。 這些元件的深度和廣度取決於資料倉儲的成熟度層級。  資料倉儲工作負載包含： 
- 將資料載入倉儲的整個程式 
- 執行資料倉儲分析和報告
- 管理資料倉儲中的資料 
- 從資料倉儲匯出資料

資料倉儲的效能處理能力取決於[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)。
- 若要查看配置給所有效能設定檔的資源，請參閱 [記憶體和並行限制] 記憶體並行-limits.md）。
- 若要調整容量，您可以相應[增加或相應減少規模](quickstart-scale-compute-portal.md)。


## <a name="workload-management-concepts"></a>工作負載管理概念
在過去，您可以透過[資源類別](resource-classes-for-workload-management.md)來管理 SQL 資料倉儲的查詢效能。  允許根據角色成員資格將記憶體指派給查詢的資源類別。  資源類別的主要挑戰在於，一旦設定之後，就不會有控制工作負載的治理或能力。  

例如，將臨機操作使用者角色成員資格授與 smallrc，可讓該使用者耗用系統上100% 的記憶體。  使用資源類別時，沒有任何方法可以保留並確保資源可用於重要的工作負載。

SQL 資料倉儲上的工作負載管理包含三個高階概念：[工作負載分類](sql-data-warehouse-workload-classification.md)、[工作負載重要性](sql-data-warehouse-workload-importance.md)和[工作負載隔離](sql-data-warehouse-workload-isolation.md)。  這些功能可讓您更充分掌控您的工作負載如何利用系統資源。

工作負載分類是將要求指派給工作負載群組和設定重要性層級的概念。  在過去，此指派是透過使用[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)的角色成員資格來完成。  這現在可以透過 [[建立工作負載] 分類](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)來完成。  分類功能提供一組更豐富的選項，例如標籤、會話和時間來分類要求。

工作負載重要性會影響要求取得資源存取權的順序。  在忙碌的系統上，具有較高重要性的要求會先存取資源。  重要性也可以確保已排序的鎖定存取。 

工作負載隔離會保留工作負載群組的資源。  工作負載群組中保留的資源會專門為該工作負載群組保留，以確保執行。  工作負載群組也可讓您定義每個要求指派的資源量，與資源類別非常類似。  工作負載群組可讓您保留或限制一組要求所能使用的資源量。  最後，工作負載群組是將規則（例如查詢超時）套用至要求的機制。  


## <a name="next-steps"></a>後續步驟

- 如需工作負載分類的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。  
- 如需工作負載隔離的詳細資訊，請參閱[工作負載隔離](sql-data-warehouse-workload-isolation.md)。  
- 如需工作負載重要性的詳細資訊，請參閱[工作負載重要性](sql-data-warehouse-workload-importance.md)。  
- 如需工作負載管理監視的詳細資訊，請參閱[工作負載管理入口網站監視](sql-data-warehouse-workload-management-portal-monitor.md)。  