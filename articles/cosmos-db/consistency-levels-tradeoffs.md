---
title: Azure Cosmos DB 一致性、可用性和效能取捨
description: 在 Azure Cosmos DB 中各種一致性層級的可用性和效能權衡取捨。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a16acfc8f9be820e9cc9b3bd59d6675b7f75d2ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445556"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>一致性、可用性與效能權衡取捨 

仰賴複寫功能來獲得高可用性和 (或) 低延遲特性的分散式資料庫必須做出權衡取捨。 所要權衡取捨的項目是讀取一致性與可用性、延遲和輸送量。

Azure Cosmos DB 會針對資料一致性提供選項頻譜。 這個方法所包含的選項比強式和最終一致性這兩個極端方法所含的選項還多。 一致性頻譜上有五個定義完善的模型可供您選擇。 最強到最弱的模型分別為：

- *強式*
- *限定過期*
- *工作階段*
- *一致前置詞*
- *最終*

每個模型都提供可用性和效能權衡取捨，並且由全方位 Sla 所支援。

## <a name="consistency-levels-and-latency"></a>一致性層級和延遲

一律保證適用於所有一致性層級的讀取延遲都會在第 99 個百分位數小於 10 毫秒。 這個讀取延遲由 SLA 所支援。 平均讀取延遲 (在第 50 個百分位數) 通常是 2 毫秒或更少。 橫跨數個區域且設定了強式一致性的 Azure Cosmos 帳戶是此保證的例外狀況。

所有一致性層級的寫入延遲一律保證在第99個百分位數小於10毫秒。 這個寫入延遲由 SLA 所支援。 平均寫入延遲 (在第 50 個百分位數) 通常是 5 毫秒或更少。

針對使用多個區域的強式一致性所設定的 Azure Cosmos 帳戶，保證寫入延遲會在兩個最遠區域之間的來回時間（RTT）之間不到兩倍，再加上10毫秒（第99個百分位數）。

確切的 RTT 延遲是一個光速距離的函式和 Azure 網路拓撲。 Azure 網路不會針對任兩個 Azure 區域之間的 RTT 提供任何延遲 SLA。 您 Azure Cosmos 帳戶的複寫延遲會顯示在 Azure 入口網站中。 您可以使用 [Azure 入口網站] （移至 [計量] 分頁）來監視與您的 Azure Cosmos 帳戶相關聯的各個區域之間的複寫延遲。

## <a name="consistency-levels-and-throughput"></a>一致性層級和輸送量

- 相較於強式和限定過期，工作階段、一致前置詞和最終一致性層級可針對相同數量的要求單位提供大約兩倍的讀取輸送量。

- 針對指定類型的寫入作業 (例如，插入、取代、更新插入和刪除)，對於要求單位的寫入輸送量會與所有一致性層級完全相同。

## <a id="rto"></a>一致性層級和資料持久性

在全域分散式資料庫環境內，當發生全區域中斷情況時，一致性層級與資料持久性之間具有直接關聯性。 當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 應用程式完全復原所需的時間，也稱為復原**時間目標**（**RTO**）。 您也必須了解在干擾性事件之後復原時，應用程式可忍受遺失的最近資料更新最大期間。 您可能會遺失的更新時間週期稱為**復原點目標**（**RPO**）。

下表定義在發生全區域中斷的情況下，一致性模型與資料持久性之間的關聯性。 請務必注意，在分散式系統中，即使使用強式一致性，也無法讓分散式資料庫的 RPO 和 RTO 為零，因為 CAP 定理。 若要深入瞭解原因，請參閱[Azure Cosmos DB 中的一致性層級](consistency-levels.md)。

|**區域**|**複寫模式**|**一致性層級**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|單一或多重主機|任何一致性層級|< 240 分鐘|< 1 週|
|> 1|單一主機|工作階段、開頭一致、最終|< 15 分鐘|< 15 分鐘|
|> 1|單一主機|限定過期|*K* & *t*|< 15 分鐘|
|> 1|單一主機|Strong|0|< 15 分鐘|
|> 1|多重主機|工作階段、開頭一致、最終|< 15 分鐘|0|
|> 1|多重主機|限定過期|*K* & *t*|0|

*K* = 專案的 *"K"* 版本（也就是更新）的數目。

*T* = 自上次更新後的時間間隔 *"T"* 。

## <a name="strong-consistency-and-multi-master"></a>強式一致性和多宿主

針對多宿主設定的 Cosmos 帳戶無法設定為強式一致性，因為分散式系統無法提供零的 RPO 和 RTO 為零。 此外，將強式一致性與多宿主搭配使用時，不會有任何寫入延遲優點，因為任何區域的寫入都必須複寫並認可至帳戶內所有已設定的區域。 這會導致與單一主帳戶相同的寫入延遲。

## <a name="next-steps"></a>後續步驟

深入了解分散式系統中的全域散發和一般一致性權衡取捨。 查看下列文章：

- [新式分散式資料庫系統設計的一致性權衡取捨](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) \(英文\)
- [高可用性](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
