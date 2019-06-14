---
title: Azure Cosmos DB 的 MongoDB 版 API 中的索引編製
description: 對於 Azure Cosmos DB 的 MongoDB 版 API 中的索引功能提供概觀。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: de037316efa50dd25ea04c370fa0e5878fb52ba1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60448498"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的 MongoDB 版 API 進行索引編製

Azure Cosmos DB 的 MongoDB 版 API 會運用 Cosmos DB 的自動索引管理功能。 因此，使用者可以存取 Cosmos DB 的預設索引原則。 所以，如果使用者未定義任何索引，或未置放任何索引，則所有欄位在插入至集合時，預設都會自動編製索引。 在大部分情況下，我們建議使用帳戶上設定的預設索引原則。

## <a name="dropping-the-default-indexes"></a>置放預設索引

下列命令可用來置放 ```coll``` 集合的預設索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>建立複合索引

複合索引可存放文件中多個欄位的參考。 就邏輯而言，它們等同於為每個欄位建立多個個別索引。 若要利用 Cosmos DB 索引技術所提供的最佳化功能，建議您建立多個個別索引，而不是單一 (非唯一) 複合索引。

## <a name="creating-unique-indexes"></a>建立唯一索引

[唯一索引](unique-keys.md)適合用於強制不會有任何兩份或多份文件包含相同的索引欄位值。 
>[!important] 
> 目前，只有在集合是空的 (不包含任何文件) 時，才能建立唯一索引。 

下列命令可在 "student_id" 欄位上建立唯一索引：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

如果是分區化集合，依據 MongoDB 行為，建立唯一索引時需要提供分區化 (分割區) 索引鍵。 換句話說，分區化集合上的所有唯一索引都是複合索引，其中有一個欄位是分割區索引鍵。

下列命令會建立分區化集合 ```coll``` (分區索引鍵是 ```university```)，其 student_id 和 university 欄位上有一個唯一索引：

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

在上述範例中，如果省略 ```"university":1``` 子句，則會傳回下列錯誤訊息：

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL 索引

若要啟用特定集合中的文件到期，則需建立[TTL 索引 (存留時間索引)](../cosmos-db/time-to-live.md)。 TTL 索引是_ts 欄位上具有 "expireAfterSeconds" 值的索引。
 
範例：
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

上述命令會導致 ```db.coll``` 集合中最近 10 秒內未經修改的文件遭到刪除。 
 
> [!NOTE]
> **_ts** 是 Cosmos DB 特有欄位，且無法從 MongoDB 用戶端存取。 這是保留 (系統) 屬性，其中包含上次修改文件的時間戳記。
>

## <a name="migrating-collections-with-indexes"></a>移轉具有索引的集合

目前，只有在集合不包含任何文件時，才可能建立唯一索引。 熱門 MongoDB 移轉工具會嘗試在匯入資料後建立唯一索引。 若要避免這個問題，建議使用者手動建立對應的集合和唯一索引，而不是允許使用移轉工具 (對於 ```mongorestore```，在命令列中使用 --noIndexRestore 旗標可完成此行為)。

## <a name="next-steps"></a>後續步驟
* [Azure Cosmos DB 中的編製索引](../cosmos-db/index-policy.md)
* [利用存留時間讓 Azure Cosmos DB 中的資料自動過期](../cosmos-db/time-to-live.md)
