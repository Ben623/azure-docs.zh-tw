---
title: 節流要求指引
description: 瞭解如何建立更好的查詢，以避免要求受到節流處理 Azure Resource Graph。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 85d68beb27ab27a2ada9acbf9482d35dec438c06
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980319"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure Resource Graph 中的節流要求指引

建立 Azure Resource Graph 資料的程式設計和頻繁使用時，應該考慮節流如何影響查詢結果。 變更要求資料的方式可協助您和您的組織避免受到節流，並維護 Azure 資源的即時資料流量。

本文涵蓋四個與在 Azure Resource Graph 中建立查詢相關的區域和模式：

- 瞭解節流標頭
- 批次處理查詢
- 錯開查詢
- 分頁的影響

## <a name="understand-throttling-headers"></a>瞭解節流標頭

Azure Resource Graph 會根據時間範圍，為每個使用者配置配額編號。 例如，使用者在每5秒的時間範圍內最多可以傳送15個查詢，而不會進行節流。 配額值是由許多因素所決定，而且可能會變更。

在每個查詢回應中，Azure Resource Graph 都會加入兩個節流標頭：

- `x-ms-user-quota-remaining` (int)：使用者的剩餘資源配額。 此值會對應至查詢計數。
- `x-ms-user-quota-resets-after` (hh:mm:ss)：重設使用者的配額耗用量前的持續時間。

為了說明標頭的作用，讓我們來看一個具有標頭和值 `x-ms-user-quota-remaining: 10` 並 `x-ms-user-quota-resets-after: 00:00:03` 的查詢回應。

- 在接下來的3秒內，最多可以提交10個查詢，而不會進行節流。
- 在3秒內，`x-ms-user-quota-remaining` 和 `x-ms-user-quota-resets-after` 的值會分別重設為 `15` 和 `00:00:05`。

若要_查看在查詢要求上使用_標頭輪詢的範例，請參閱[平行查詢](#query-in-parallel)中的範例。

## <a name="batching-queries"></a>批次處理查詢

依訂用帳戶、資源群組或個別資源批次處理查詢，會比平行處理查詢更有效率。 較大查詢的配額成本通常小於許多小型和目標查詢的配額成本。 建議將批次大小設為小於_300_。

- 不佳的最佳方法範例

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 優化批次處理方法的範例 #1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 優化批次處理方法的範例 #2

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>錯開查詢

由於強制執行節流的方式，因此建議您將查詢錯開。 也就是說，不會同時傳送60查詢，而是將查詢錯開成四個5秒的視窗：

- 非交錯查詢排程

  | 查詢計數         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 時間間隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

- 錯開的查詢排程

  | 查詢計數         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 時間間隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

以下是在查詢 Azure Resource Graph 時遵循節流標頭的範例：

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>平行查詢

雖然建議透過平行處理來進行批次處理，但有時無法輕鬆地批次處理查詢。 在這些情況下，您可能會想要以平行方式傳送多個查詢來查詢 Azure Resource Graph。 以下範例說明如何根據這類案例中的節流標_頭來進行_輪詢：

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>分頁

因為 Azure Resource Graph 在單一查詢回應中最多傳回1000個專案，所以您可能需要將查詢[分頁](./work-with-data.md#paging-results)，以取得您要尋找的完整資料集。 不過，某些 Azure Resource Graph 的用戶端處理分頁的方式與其他不同。

- C# SDK

  使用 Az.resourcegraph SDK 時，您必須將先前查詢回應所傳回的 skip token 傳遞至下一個分頁查詢，以處理分頁。 這種設計表示您需要從所有分頁呼叫中收集結果，並將它們結合在一起。 在此情況下，您傳送的每個分頁查詢都會採用一個查詢配額：

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  當您使用 Azure CLI 或 Azure PowerShell 時，Azure Resource Graph 的查詢會自動編頁以提取最多5000個專案。 查詢結果會從所有分頁呼叫傳回結合的專案清單。 在此情況下，視查詢結果中的專案數而定，單一分頁查詢可能會耗用一個以上的查詢配額。 例如，在下列範例中，查詢的單一執行可能會耗用最多五個查詢配額：

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>仍會受到節流？

如果您在採用上述建議之後受到節流，請聯絡小組[resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)。

提供下列詳細資料：

- 您的特定使用案例和商務驅動程式需要更高的節流限制。
- 您有多少資源可以存取？ 有多少會從單一查詢傳回？
- 您感興趣的資源類型為何？
- 您的查詢模式是什麼？ X 每 Y 秒查詢等

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言。
- 請參閱 advanced[查詢](../samples/advanced.md)中的 advanced 使用。
- 瞭解如何[探索資源](explore-resources.md)。