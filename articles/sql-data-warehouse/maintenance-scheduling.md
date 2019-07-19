---
title: Azure 維護排程 (預覽版) | Microsoft Docs
description: 維護排程可讓客戶規劃必要的排程維護事件，Azure SQL 資料倉儲服務會使用它們來推出新功能、升級與修補程式。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 3875106e8c6301c95bc8d0fbce6a1c0400d07f78
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278115"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>使用維護排程管理服務更新和維護

維護排程現在可在所有 Azure SQL 資料倉儲區域中使用。 此功能會整合服務健康狀態計劃性維護通知、資源健康狀態檢查監視器，以及 Azure SQL 資料倉儲維護排程服務。

您會使用維護排程來選擇時段，方便您接收新功能、升級與修補程式。 您可以在七天期間內選擇主要和次要維護時段。 範例中的主要時段是星期六 22:00 到星期日 01:00，而次要時段為星期三 19:00 到 22:00。 如果 SQL 資料倉儲無法在主要維護時段執行維護，它將在次要維護時段再次試著進行維護。 服務維護可能會在主要和次要時段發生。 為了確保能夠快速完成所有維護作業, DW400 (c) 和較低的資料倉儲層可以在指定的維護期間以外完成維護。

所有新建立的 Azure SQL 資料倉儲執行個體在部署期間都會套用一個系統定義的維護排程。 一旦部署完畢，就可以編輯排程。

每個維護時段都可以是三到八個小時。 維護可能會在該時段內的任何時間進行。 當維護開始時, 將會取消所有作用中的會話, 並回復未認可的交易。 因為服務會將新的程式碼部署至您的資料倉儲, 所以您應該會預期在連線時有多短暫的中斷 您將會在資料倉儲上的維護完成後立即收到通知

若要使用這項功能，您必須在個別的日期範圍內找出主要和次要時段。 所有的維護作業都應該在排程的維護時段內完成。 如果沒有事先通知，就不會在指定的維護時段以外進行維護。 如果您的資料倉儲已在排程維護期間暫停，它將會在繼續作業期間更新。  

## <a name="alerts-and-monitoring"></a>警示和監視

服務健康狀態通知與資源健康狀態檢查監視器的整合，可讓客戶隨時掌握即將進行的維護活動。 新的自動化會利用 Azure 監視器。 您可以決定您想要收到即將進行維護事件通知的方式。 也請決定哪一個自動化流程可協助您管理停機時間，並將對您作業的影響降至最低。

24小時提前通知會在所有維護事件之前, 目前的例外狀況是 DW400c 和較低的層級。 若要將執行個體的停機時間降至最低，先確定資料倉儲在您選擇的維護期間之前沒有長時間執行的交易。

> [!NOTE]
> 在此事件中, 我們必須部署時間緊迫的更新, 可能會大幅降低提前通知時間。

如果您收到維護將進行的預先通知，但 SQL 資料倉儲無法在這段期間內執行維護，則您將收到取消通知。 之後，維護將會在下一個排程維護期間繼續。

所有作用中的維護事件都會顯示在 [服務健康狀態 - 計劃性維護]  區段中。 服務健康狀態歷程記錄包含過去事件的完整記錄。 您可以在作用中的事件期間，透過 Azure 服務健康狀態檢查入口網站儀表板來監視維護。

### <a name="maintenance-schedule-availability"></a>維護排程可用性

即使維護排程尚無法在您所選的區域中使用，但您可以隨時檢視和編輯維護排程。 當維護排程在您的區域中變成可用時，已識別的排程將在您的資料倉儲上立即變成作用中。

## <a name="next-steps"></a>後續步驟

- [深入了解](viewing-maintenance-schedule.md)如何檢視維護排程。
- [深入了解](changing-maintenance-schedule.md)如何變更維護排程。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)如何使用 Azure 監視器來建立、檢視和管理警示。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)適用於記錄警示規則的 Webhook 動作。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)如何建立及管理動作群組。
- [深入了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服務健康狀態。
