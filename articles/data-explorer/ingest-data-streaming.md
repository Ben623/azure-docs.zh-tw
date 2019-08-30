---
title: 使用串流內嵌將資料內嵌至 Azure 資料總管
description: 瞭解如何使用串流內嵌, 將資料內嵌 (載入) 至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: b716cbf3efb044da68d4dd1dcb724369855d1ed1
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173647"
---
# <a name="streaming-ingestion-preview"></a>串流內嵌 (預覽)

串流內嵌的目標是針對不同的磁片區資料, 在需要低延遲的情況下, 有一段時間小於10秒的案例。 它是用來優化許多資料表的工作處理, 在一或多個資料庫中, 每個資料表中的資料流程相對較小 (每秒少筆記錄), 但整體資料內嵌磁片區為高 (每秒數千筆記錄)。

當資料量成長到每個資料表每秒 1 MB 以上時, 請使用傳統 (大量) 內嵌, 而不是串流內嵌。 閱讀[資料內嵌總覽](/azure/data-explorer/ingest-data-overview), 以深入瞭解內嵌的各種方法。

> [!NOTE]
> 串流內嵌不支援下列功能:
> * [資料庫資料指標](/azure/kusto/management/databasecursor)。
> * [資料對應](/azure/kusto/management/mappings)。 僅支援[預先建立的](/azure/kusto/management/tables#create-ingestion-mapping)資料對應。 

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入[WEB UI](https://dataexplorer.azure.com/)。
* 建立[Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>在您的叢集中啟用串流內嵌

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 [設定]中, 選取 [**設定**]。 
1. 在 [設定] 窗格中, 選取 [**開啟**] 以啟用**串流**內嵌。
1. 選取 [ **儲存**]。
 
    ![串流內嵌于](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. 在[WEB UI](https://dataexplorer.azure.com/)中, 定義將接收串流資料之資料表或資料庫的[串流內嵌原則](/azure/kusto/concepts/streamingingestionpolicy)。 

    > [!TIP]
    > 如果原則是在資料庫層級定義, 則資料庫中的所有資料表都會啟用串流內嵌。

## <a name="supported-streaming-ingestion-types"></a>支援的串流內嵌類型

支援的串流內嵌類型有兩種:

* 當做資料來源使用的[事件中樞](/azure/data-explorer/ingest-data-event-hub)
* 自訂內嵌需要您撰寫使用其中一個 Azure 資料總管用戶端程式庫的應用程式。 如需範例應用程式, 請參閱[串流內嵌範例](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>選擇適當的串流內嵌類型

|   |事件中樞  |自訂內嵌  |
|---------|---------|---------|
|內嵌初始化與可供查詢的資料之間的資料延遲   |    較長延遲     |   延遲較短      |
|開發額外負荷    |   快速且輕鬆的設定, 無需任何開發負擔    |   應用程式的高開發額外負荷, 可處理錯誤並確保資料一致性     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>停用叢集上的串流內嵌

> [!WARNING]
> 停用串流內嵌可能需要幾個小時的時間。

1. 從所有相關的資料表和資料庫中卸載[串流內嵌原則](/azure/kusto/concepts/streamingingestionpolicy)。 串流的內嵌原則移除會觸發從初始儲存體到資料行存放區 (範圍或分區) 中的永久儲存體的串流內嵌資料移動。 視初始儲存體中的資料量和叢集的 CPU 和記憶體使用量而定, 資料移動可以在數秒到幾個小時之間持續。
1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 [設定]中, 選取 [**設定**]。 
1. 在 [設定] 窗格中, 選取 [**關閉**] 以停用**串流**內嵌。
1. 選取 [ **儲存**]。

    ![串流內嵌已關閉](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>限制

* 透過增加的 VM 和叢集大小, 串流處理內嵌的效能和容量規模。 針對單一 D11 節點, 建議的負載是每秒最多20個要求。 針對單一 D14 節點, 建議的負載是每秒最多150個要求。
* 目前, 支援僅適用于8和16核心 Sku (D13、D14、L8 和 L16 也)。
* 每個內嵌要求的資料大小限制為 4 MB。
* 架構更新 (例如建立和修改資料表和內嵌對應) 最多可能需要5分鐘的時間來處理串流內嵌服務。
* 在叢集上啟用串流內嵌, 即使資料不是透過串流內嵌, 也會使用叢集機器的部分本機 SSD 磁片來串流內嵌資料, 並減少用於熱快取的儲存體。

## <a name="next-steps"></a>後續步驟

* [查詢 Azure 中的資料資料總管](web-query-data.md)