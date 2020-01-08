---
title: 在 Azure Cosmos DB 中執行自訂同步處理
description: 了解如何實作自訂同步處理，透過最佳化處理而在 Azure Cosmos DB 中獲得較高的可用性和效能。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 2c989b352ef1b7800980c3a89b007c625198f822
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441807"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>實作自訂同步處理，透過最佳化處理而獲得較高的可用性和效能

Azure Cosmos DB 提供[五個定義完善的一致性層級](consistency-levels.md)供您選擇，以讓您在一致性、效能和可用性之間做出平衡的取捨。 強式一致性有助於確保資料在 Azure Cosmos 帳戶可供使用的每個區域中能夠同步複寫並保有持久性。 此設定雖可提供最高層級的持久性，但會犧牲效能和可用性。 如果您要應用程式控制/放寬資料持久性，以符合應用程式需求又不損及可用性，您可以在應用程式層採用「自訂的同步處理」，以實現所需的持久性層級。

下圖以視覺方式說明自訂同步處理模型：

![自訂同步處理](./media/how-to-custom-synchronization/custom-synchronization.png)

在此案例中，Azure Cosmos 容器會複寫到位於全球各地的數個區域，範圍橫跨多個大陸。 在此案例中的所有區域使用強式一致性將會對效能造成影響。 為了確保有較高的資料持久性層級，又不會損及寫入延遲時間，應用程式可以使用兩個共用相同[工作階段權杖](how-to-manage-consistency.md#utilize-session-tokens)的用戶端。

第一個用戶端可以將資料寫入至本機區域 (例如，美國西部)。 第二個用戶端 (例如，在美國東部) 則是用來確保同步處理的讀取用戶端。 藉由讓工作階段權杖從寫入回應傳到下列讀取中，讀取會確保寫入會同步處理至美國東部。 Azure Cosmos DB 可確保寫入至少會讓一個區域看見。 如果原始寫入區域停止運作，這些寫入保證不受區域性中斷的影響。 在此案例中，每個寫入都會同步處理至美國東部，從而降低在所有區域採用強式一致性所造成的延遲。 在多主機案例中，每個區域都會發生寫入，您可將此模型擴充為會以平行方式同步至多個區域。

## <a name="configure-the-clients"></a>設定用戶端

下列範例會示範具現化兩個用戶端以進行自訂同步的資料存取層：

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>實作自訂同步處理

用戶端完成初始化後，應用程式便可執行對本機區域 (美國西部) 的寫入，並可強制將寫入同步處理至美國東部，如下所示。

### <a name="net-v2-sdk"></a>.Net V2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

您可以擴充模型，以平行地同步至多個區域。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Cosmos DB 中的全域散發和一致性，請閱讀下列文章：

* [在 Azure Cosmos DB 中選擇正確的一致性層級](consistency-levels-choosing.md)
* [Azure Cosmos DB 中一致性、可用性和效能的取捨](consistency-levels-tradeoffs.md)
* [在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md)
* [Azure Cosmos DB 中的資料分割和資料散發](partition-data.md)
