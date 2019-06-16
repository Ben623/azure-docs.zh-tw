---
title: 專用事件中樞概觀 - Azure 事件中樞 | Microsoft Docs
description: 這篇文章會提供專用的 Azure 事件中樞，這可提供事件中樞的單一租用戶部署的概觀。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 52e092e6e48f004656860cb5d078e780039584ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730230"
---
# <a name="overview-of-event-hubs-dedicated"></a>事件中樞專用的概觀

*事件中樞叢集*提供客戶最嚴苛的資料流處理需求的單一租用戶部署。 此單一租用戶供應項目具有保證的 99.99 %sla，而且僅適用於我們的定價層的專用。 事件中樞叢集可輸入數百萬個事件每秒容量保證與秒的延遲。 命名空間和事件中樞建立專用的叢集內包含標準的供應項目及其他資源，但沒有任何輸入限制的所有功能。 它也包含熱門[事件中樞擷取](event-hubs-capture-overview.md)，完全免費，可讓您將會自動批次和記錄資料串流至 Azure 儲存體或 Azure Data Lake 的功能。 

叢集進行佈建和依計費**容量單位 (Cu)** ，預先配置的 CPU 和記憶體資源數量。 您可以為每個叢集購買 1、2、4、8、12、16 或 20 個 CU。 您可以在多少內嵌和串流處理每個 CU 取決於各種因素，例如生產者和取用者，裝載圖形輸出數目評等 （請參閱下方的基準測試結果，如需詳細資訊）。 

> [!NOTE]
> 所有事件中樞叢集依預設啟用 Kafka，而且支援 Kafka 端點可以使用您現有的 Kafka 架構的應用程式。 需要啟用上的 Kafka 叢集不會影響您的非 Kafka 使用案例;沒有任何選項或停用叢集上的 Kafka 需要。

## <a name="why-dedicated"></a>為什麼專用嗎？

專用的事件中樞提供三個吸引人的優點，需要企業級容量的客戶：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>單一租用戶保證容量的較佳的效能

專用的叢集可保證在完整規模的容量，可以輸入最多 gb 的資料流處理的資料，以容納任何完全持久的儲存體與低於一秒延遲暴增的流量。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>內含和專有功能的存取權 
專用的供應項目包含功能，例如擷取任何額外的費用，以及即將推出的功能，例如 BYOK 的獨佔存取權。 服務也管理負載平衡、 作業系統更新、 安全性修補程式和客戶的資料分割，以便您可以花較少的時間在基礎結構維護和更多的時間，在建置用戶端的功能。  

#### <a name="cost-savings"></a>節省成本
高輸入磁碟區 (> 100 的 Tu)，叢集成本遠小於每小時購買相當數量的輸送量單位，標準的供應項目。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>事件中樞專用配額和限制

事件中樞專用供應項目是固定的每月價格，並至少 4 小時的使用量計費。 專用的層提供所有功能的 「 標準 」 方案，但企業規模容量和限制嚴苛的工作負載的客戶。 

| 功能 | 標準 | 专用 |
| --- |:---:|:---:|
| 頻寬 | 20 Tu （最多 40 Tu) | 20 Cu |
| 命名空間 |  1 | 每個 CU 50 |
| 事件中樞 |  每個命名空間的 10 | 每個命名空間的 1000 |
| 輸入事件 | 按百萬個事件付費 | 已包括 |
| 訊息大小 | 1 百萬個位元組 | 1 百萬個位元組 |
| 分割數 | 每個命名空間的 40 | 每個 CU 2000 |
| 用戶群組 | 每一個事件中樞的 20 | 無限制每個 CU，每一個事件中樞的 1000 |
| 代理連線 | 包含 1000，最多 5,000 | 包含 100k 和 max |
| 訊息保留期 | 7 天，每個 TU 內含 84 GB | 90 天，每個 CU 包含 10 TB |
| 擷取 | 按小時付費 | 已包括 |

## <a name="how-to-onboard"></a>如何加入

自助式體驗[建立事件中樞叢集](event-hubs-dedicated-cluster-create-portal.md)透過[Azure 入口網站](https://aka.ms/eventhubsclusterquickstart)現在處於預覽狀態。 如果您有任何疑問或需要協助上架到事件中樞專用時，請連絡[事件中樞小組](mailto:askeventhubs@microsoft.com)。

## <a name="faqs"></a>常見問題集

#### <a name="what-can-i-achieve-with-a-cluster"></a>可以達到什麼與叢集？

事件中樞叢集，您可以在多少內嵌和串流處理取決於各種因素，例如您生產者、 取用者，您會擷取與處理、 速率及更多。 

下表顯示我們在測試期間達成的基準測試結果：

| 承載圖形 | 接收者 | 輸入頻寬| 輸入訊息 | 輸出頻寬 | 輸出訊息 | TU 總計 | 每個 CU 的 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 的批次 | 2 | 400 MB/秒 | 400 萬訊息數/秒 | 800 MB/秒 | 800 k 訊息數/秒 | 400 個 TU | 100 個 TU | 
| 10x10KB 的批次 | 2 | 666 MB/秒 | 66.6k messages/sec | 1.33 GB/秒 | 133 k 訊息數/秒 | 666 個 TU | 166 個 TU |
| 6x32KB 的批次 | 1 | 1.05 GB/秒 | 34 個訊息 / 秒 | 1.05 GB/秒 | 34 k 訊息數/秒 | 1000 個 TU | 250 個 TU |

測試期間使用了下列準則：

- 使用四個容量單位 (Cu) 的專用層事件中樞叢集。 
- 用於擷取的事件中樞具有 200 個分割區。 
- 已擷取的資料會透過接收自所有分割區的兩個接收者應用程式來接收。

#### <a name="can-i-scale-updown-my-cluster"></a>我可以相應增加/減少我的叢集嗎？

建立之後，叢集會收取最少 4 小時的使用量而定。 在自助式體驗的預覽版本中，您可以提交[支援要求](https://ms.portal.azure.com/#create/Microsoft.Support)向下事件中樞小組*技術 > 配額 > 要求相應增加或減少專用叢集的規模*標尺增加或相應減少叢集。 可能需要最多 7 天，以完成相應減少叢集的要求。 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>如何將異地災害復原使用我的叢集？

您可以地理配對專用層叢集底下的命名空間，與專用層叢集底下的另一個命名空間。 不建議配對的命名空間中我們標準供應項目，由於輸送量限制會為不相容錯誤使得專用層命名空間。 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>可以移轉我的標準命名空間，以屬於專用層叢集嗎？
我們目前不支援自動的移轉程序從標準的命名空間，您的事件中樞資料移轉到其中一個專用。 

## <a name="next-steps"></a>後續步驟

請連絡您的 Microsoft 業務代表或 Microsoft 支援服務以取得有關事件中樞專用的其他詳細資料。 您也可以建立叢集，或深入了解事件中樞定價層，請造訪下列連結：

- [建立事件中樞叢集透過 Azure 入口網站](https://aka.ms/eventhubsclusterquickstart) 
- [事件中樞專用價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 您也可以連絡 Microsoft 銷售代表或 Microsoft 支援服務，以取得事件中樞專用容量的其他詳細資料。
- [事件中樞常見問題集](event-hubs-faq.md)包含價格資訊，並提供一些常見問題的解答。
