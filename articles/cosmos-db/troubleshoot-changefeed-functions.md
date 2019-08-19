---
title: 針對 Cosmos DB 使用 Azure Functions 觸發程式時, 診斷並疑難排解問題
description: 使用 Cosmos DB 的 Azure Functions 觸發程式時, 常見的問題、解決方法和診斷步驟
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 17fa443c3b0113d80a020f2a43c7099cf5a832d2
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772892"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>針對 Cosmos DB 使用 Azure Functions 觸發程式時, 診斷並疑難排解問題

本文涵蓋當您使用[Cosmos DB 的 Azure Functions 觸發程式](change-feed-functions.md)時, 常見的問題、解決方法和診斷步驟。

## <a name="dependencies"></a>相依性

Cosmos DB 的 Azure Functions 觸發程式和系結相依于基底 Azure Functions 執行時間上的延伸模組套件。 請一律保持這些套件的更新, 因為它們可能包含修正和新功能, 可解決您可能會遇到的任何潛在問題:

* 如 Azure Functions V2, 請參閱[CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。
* 如 Azure Functions V1, 請參閱 < [Microsoft Azure web.config. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)。

除非明確指定, 否則本文一律會在每次提及執行時間時參考 Azure Functions V2。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>獨立使用 Azure Cosmos DB SDK

延伸模組套件的主要功能是為 Cosmos DB 提供 Azure Functions 觸發程式和系結的支援。 它也包含[Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), 如果您想要以程式設計方式與 Azure Cosmos DB 互動, 而不使用觸發程式和系結, 這會很有説明。

如果想要使用 Azure Cosmos DB SDK, 請確定您未新增至您的專案另一個 NuGet 套件參考。 相反地,**讓 SDK 參考解析 Azure Functions 的延伸模組套件**。 與觸發程式和系結分開使用 Azure Cosmos DB SDK

此外, 如果您要手動建立自己的[AZURE COSMOS DB SDK 用戶端](./sql-api-sdk-dotnet-core.md)實例, 您應該遵循只有一個[使用單一模式方法](../azure-functions/manage-connections.md#documentclient-code-example-c)的用戶端實例的模式。 此程式可避免您的作業中可能的通訊端問題。

## <a name="common-scenarios-and-workarounds"></a>常見案例和因應措施

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure 函數失敗, 錯誤訊息集合不存在

Azure 函數失敗, 並出現錯誤訊息: 「來源集合 ' collection-name ' (在資料庫 ' database-name ' 中) 或租用集合 ' collection2-name ' (在資料庫 ' database2-name ' 中) 不存在。 在接聽程式啟動之前, 這兩個集合都必須存在。 若要自動建立租用集合, 請將 ' CreateLeaseCollectionIfNotExists ' 設定為 ' true '」

這表示觸發程式運作所需的其中一個或兩個 Azure Cosmos 容器不存在, 或無法連線到 Azure 函式。 **錯誤本身會告訴您, 哪一個 Azure Cosmos 資料庫和容器是**根據您的設定來尋找的觸發程式。

1. 請驗證屬性, 並**參考存在於 Azure 函數應用程式中的設定。** `ConnectionStringSetting` 這個屬性的值不應該是連接字串本身, 而是設定的名稱。
2. `databaseName`確認和`collectionName`存在於您的 Azure Cosmos 帳戶中。 如果您使用自動值取代 (使用`%settingName%`模式), 請確定您的 Azure 函數應用程式中有該設定的名稱。
3. 如果您未指定`LeaseCollectionName/leaseCollectionName`, 則預設值為「租用」。 確認這類容器存在。 (選擇性) 您可以`CreateLeaseCollectionIfNotExists`將`true`觸發程式中的屬性設定為, 以自動加以建立。
4. 請確認您的[Azure Cosmos 帳戶的防火牆](how-to-configure-firewall.md)設定, 以查看其是否不會封鎖 azure 函式。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 函數無法啟動, 因為「共用輸送量集合應該有分割區索引鍵」

舊版的 Azure Cosmos DB 擴充功能不支援使用在[共用輸送量資料庫](./set-throughput.md#set-throughput-on-a-database)內建立的租用容器。 若要解決此問題, 請更新[CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)延伸模組, 以取得最新版本。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 函式的開頭不能是「租用集合, 如果已分割, 則必須有等於 id 的分割區索引鍵」。

此錯誤表示您目前的租用容器已分割, 但資料分割索引鍵路徑不`/id`是。 若要解決此問題, 您必須使用`/id`做為分割區索引鍵來重新建立租用容器。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>您會看到「值不能為 null。 當您嘗試執行觸發程式時, Azure Functions 記錄中的參數名稱: o "

如果您使用 Azure 入口網站, 而且您在檢查使用觸發程式的 Azure 函式時, 嘗試在畫面上選取 [**執行**] 按鈕, 就會出現此問題。 觸發程式不需要您選取 [執行] 來啟動, 它會在部署 Azure 函式時自動啟動。 如果您想要在 Azure 入口網站上檢查 Azure 函式的記錄資料流程, 只要移至您的受監視容器並插入一些新專案, 您就會自動看到執行的觸發程式。

### <a name="my-changes-take-too-long-be-received"></a>我的變更收到的時間太長

此案例可能會有多個原因, 而且應該檢查所有專案:

1. 您的 Azure 函數是否部署在與 Azure Cosmos 帳戶相同的區域中？ 為了達到最佳的網路延遲, Azure 函式和您的 Azure Cosmos 帳戶都應該共存于相同的 Azure 區域中。
2. 您的 Azure Cosmos 容器中的變更會持續或偶爾發生嗎？
如果是後者, 則會在所儲存的變更與 Azure 函式進行挑選之間有一些延遲。 這是因為在內部, 當觸發程式檢查 Azure Cosmos 容器中的變更, 並找不到要讀取的暫止時, 它在檢查是否有新的變更之前, 會進入睡眠狀態 (預設為5秒), 以避免高 RU 耗用量。 您可以使用觸發程式[設定](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)中的`FeedPollDelay/feedPollDelay`設定來設定此睡眠時間 (值應以毫秒為單位)。
3. 您的 Azure Cosmos 容器可能會受到[速率限制](./request-units.md)。
4. 您可以使用觸發`PreferredLocations`程式中的屬性來指定以逗號分隔的 Azure 區域清單, 以定義自訂的慣用連接順序。

### <a name="some-changes-are-missing-in-my-trigger"></a>我的觸發程式中遺漏了某些變更

如果您發現 Azure 函式未挑選 Azure Cosmos 容器中所發生的某些變更, 則需要進行初始調查步驟。」

當您的 Azure 函式收到變更時, 通常會加以處理, 而且可以選擇性地將結果傳送到另一個目的地。 當您調查遺失的變更時, 請務必**測量要在內嵌點接收的變更**(當 Azure 函式啟動時), 而不是在目的地上。

如果目的地上遺失某些變更, 這可能表示在收到變更之後, Azure 函式執行期間發生了一些錯誤。

在此案例中, 最佳作法是在您的程式`try/catch blocks`代碼中, 以及可能正在處理變更的迴圈內加入, 以偵測特定專案子集的任何失敗, 並據以處理 (將它們傳送至其他儲存體以供進一步使用)分析或重試)。 

> [!NOTE]
> 根據預設, Cosmos DB 的 Azure Functions 觸發程式在程式碼執行期間發生未處理的例外狀況時, 不會重試一批變更。 這表示變更未抵達目的地的原因是因為您無法處理它們。

如果您發現您的觸發程式完全不會收到某些變更, 最常見的情況就是**另一個 Azure 函數正在**執行。 它可能是部署在 Azure 中的另一個 Azure 函式, 或是在具有**完全相同**設定 (相同的受監視和租用容器) 的開發人員電腦本機上執行的 azure function, 而此 Azure function 會竊取您所做的變更子集會預期您的 Azure 函式會進行處理。

此外, 如果您知道您正在執行多少個 Azure 函數應用程式實例, 就可以驗證此案例。 如果您檢查租用容器並計算中的租用專案數, 則其中`Owner`屬性的相異值應該等於您函數應用程式的實例數目。 如果擁有者超過已知的 Azure 函數應用程式實例, 則表示這些額外的擁有者是「竊取」變更的人。

解決這種情況的一個簡單方法, 就是使用`LeaseCollectionPrefix/leaseCollectionPrefix`新的/不同的值將套用至您的函式, 或者使用新的租用容器來測試。

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>需要從頭開始重新開機並重新處理我的容器中的所有專案 
從一開始就重新處理容器中的所有專案:
1. 如果您的 Azure 函式目前正在執行, 請將它停止。 
1. 刪除租用集合中的檔 (或刪除並重新建立租用集合, 使其空白)
1. 將函數中的[StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) CosmosDBTrigger 屬性設定為 true。 
1. 重新開機 Azure function。 它現在會從一開始就讀取和處理所有變更。 

將[StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)設定為 true 時, 會告訴 Azure 函式從集合歷程記錄的開頭開始讀取變更, 而不是目前的時間。 這僅適用于沒有已建立的租用時 (也就是租用集合中的檔)。 當已經建立租用時, 將此屬性設定為 true 不會有任何作用;在此情況下, 當函式停止並重新啟動時, 它會從最後一個檢查點開始讀取, 如租用集合中所定義。 若要從頭重新處理, 請遵循上述步驟1-4。  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>只能使用 IReadOnlyList\<檔 > 或 JArray 來進行系結

如果您的 Azure Functions 專案 (或任何參考的專案) 包含與[Azure Functions Cosmos DB 延伸](./troubleshoot-changefeed-functions.md#dependencies)模組所提供不同版本之 Azure Cosmos DB SDK 的手動 NuGet 參考, 就會發生此錯誤。

若要解決這種情況, 請移除已新增的手動 NuGet 參考, 然後讓 Azure Cosmos DB SDK 參考透過 Azure Functions Cosmos DB 延伸模組套件來解析。

## <a name="next-steps"></a>後續步驟

* [啟用 Azure Functions 的監視](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK 疑難排解](./troubleshoot-dot-net-sdk.md)
