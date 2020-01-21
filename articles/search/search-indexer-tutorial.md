---
title: 教學課程：使用 C# 為 Azure SQL 資料庫中的資料編製索引
titleSuffix: Azure Cognitive Search
description: 在此 C# 教學課程中，連線至 Azure SQL 資料庫、擷取可搜尋的資料，並將其載入至 Azure 認知搜尋服務索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 1b03f5569386212905cdeb362cfe0a88774eb887
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754348"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>教學課程：透過 Azure 認知搜尋索引子來使用 C# 匯入 Azure SQL 資料庫

了解如何設定索引子，以便從範例 Azure SQL 資料庫擷取可搜尋的資料。 [索引子](search-indexer-overview.md)是 Azure 認知搜尋的元件，可搜耙外部資料來源，以內容填入[搜尋索引](search-what-is-an-index.md)。 在所有索引子中，最廣泛使用的是 Azure SQL Database 的索引子。 

索引子設定的熟練度很有幫助，因為它可簡化您必須撰寫和維護的程式碼數量。 您不需準備及推送結構描述相容的 JSON 資料集，而是可以將索引子附加至資料來源，讓索引子擷取資料並將其插入索引中，並選擇性地依照週期性排程執行索引子，以掌握基礎來源中的變更。

在本教學課程中，使用 [Azure 認知搜尋 .NET 用戶端程式庫](https://aka.ms/search-sdk)和 .NET Core 主控台應用程式，執行下列工作：

> [!div class="checklist"]
> * 將搜尋服務資訊新增至應用程式設定
> * 在 Azure SQL 資料庫中準備外部資料集 
> * 檢閱範例程式碼中的索引和索引子定義
> * 執行索引子程式碼以匯入資料
> * 搜尋索引
> * 在入口網站中檢視索引子組態

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

本快速入門會使用下列服務、工具和資料。 

[建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 會儲存索引子所用的外部資料來源。 範例解決方案會提供 SQL 資料檔案，以建立資料表。 本教學課程會提供建立服務和資料庫的步驟。

任何版本的 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 都可用來執行解決方案範例。 範例程式碼和指示已在免費的 Community 版本上經過測試。

位在 Azure 範例 GitHub 存放庫中的 [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) 會提供解決方案範例。 下載並擷取解決方案。 根據預設，解決方案會是唯讀狀態。 以滑鼠右鍵按一下方案並清除唯讀屬性，以便您修改檔案。

> [!Note]
> 如果您使用免費的 Azure 認知搜尋服務，則會有三個索引、三個索引子，以及三個資料來源的限制。 本教學課程會各建立一個。 確定您的服務有空間可接受新的資源。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]   >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="set-up-connections"></a>設定連線
必要服務的連線資訊會指定於方案的 **appsettings.json** 檔案中。 

1. 在 Visual Studio 中，開啟 **DotNetHowToIndexers.sln**檔案。

1. 在 [方案總管] 中，開啟 **appsettings.json**，以便您填入每項設定。  

您現在就可以使用 Azure 認知搜尋服務的 URL 和管理員金鑰來填入前兩個項目。 假設端點是 `https://mydemo.search.windows.net`，則要提供的服務名稱就是 `mydemo`。

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

最後一個項目需要現有的資料庫。 您將下一步中建立此資料庫。

## <a name="prepare-sample-data"></a>準備範例資料

在此步驟中，建立索引子可以搜耙的外部資料來源。 您可以使用 Azure 入口網站和範例中的 hotels.sql  檔案，在 Azure SQL Database 中建立資料集。 Azure 認知搜尋會取用扁平化資料列集，例如從檢視或查詢產生的資料列集。 範例方案中的 SQL 檔案會建立並填入單一資料表。

下列練習假設沒有任何現有的伺服器或資料庫，並指示您在步驟 2 中建立兩者。 (選擇性) 如果您有現有的資源，您可以在其中新增 hotels 資料表 (在步驟 4 開始)。

1. [登入 Azure 入口網站](https://portal.azure.com/)。 

2. 尋找或建立 **Azure SQL Database**，以建立資料庫、伺服器和資源群組。 您可以使用預設值和最低層級的定價層。 建立伺服器的優點之一是您可以指定系統管理員使用者名稱和密碼，以便在稍後步驟中建立和載入資料表。

   ![新增資料庫頁面](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. 按一下 [建立]  來部署新的伺服器和資料庫。 等候部署伺服器和資料庫。

4. 開啟新資料庫的 [SQL Database] 頁面 (如果尚未開啟的話)。 資源名稱應該為 SQL Database  ，而非 SQL Server  。

   ![SQL 資料庫頁面](./media/search-indexer-tutorial/hotels-db.png)

4. 在導覽窗格上，按一下 [查詢編輯器 (預覽)]  。

5. 按一下 [登入]  ，並輸入伺服器管理員的使用者名稱和密碼。

6. 按一下 [開啟查詢]  並瀏覽至 hotels.sql  的位置。 

7. 選取檔案，然後按一下 [開啟]  。 指令碼應該會看起來如下列螢幕擷取畫面所示：

   ![SQL 指令碼](./media/search-indexer-tutorial/sql-script.png)

8. 按一下 [執行]  來執行查詢。 在 [結果] 窗格中，您應會看到查詢成功訊息 (3 個資料列)。

9. 若要從這個資料表傳回資料列集，您可以執行下列查詢作為驗證步驟：

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    典型查詢 `SELECT * FROM Hotels` 不適用於查詢編輯器中。 範例資料包含 [位置] 欄位中的地理座標，目前並未在編輯器中處理該欄位。 如需要查詢的其他資料行清單，您可以執行此陳述式：`SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. 您現在有外部資料集，請複製資料庫的 ADO.NET 連接字串。 在您資料庫的 [SQL Database] 頁面上，移至 [設定]   > [連接字串]  ，並複製 ADO.NET 連接字串。
 
    ADO.NET 連接字串如下列範例所示，已修改成使用有效的資料庫名稱、使用者名稱和密碼。

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. 將連接字串貼到 "AzureSqlConnectionString" 中，作為 Visual Studio 的 **appsettings.json** 檔案中的第三個項目。

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>了解程式碼

當資料和組態設定都就緒之後，即可建置和執行 **DotNetHowToIndexers.sln** 中的範例程式。 這麼做之前，請花一分鐘研究此範例中的索引和索引子定義。 相關程式碼位於兩個檔案中：

  + **hotel.cs**，內含可定義索引的結構描述
  + **Program.cs**，內含用於建立和管理您服務中結構的函式

### <a name="in-hotelcs"></a>在 hotel.cs 中

索引結構描述會定義欄位集合，包括用於指定允許作業的屬性，例如欄位是否可全文檢索搜尋、可篩選，或可排序 (如 HotelName 的下列欄位定義中所示)。 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

結構描述也可以包含其他元素，包括提高搜尋分數、自訂分析城市和其他建構的評分設定檔。 不過，基於我們的目的，結構描述並未嚴密定義，只包含在範例資料集中找到的欄位。

在本教學課程中，索引子會從一個資料來源提取資料。 實際上，您可以將多個索引子附加至相同的索引，並從多個資料來源建立合併的可搜尋索引。 根據您需要彈性的狀況而定，您可以使用相同的索引-索引子配對、不同的資料來源組合，或具有各種索引子和資料來源組合的一個索引。

### <a name="in-programcs"></a>在 Program.cs 中

主要程式包含用於建立用戶端、索引、資料來源和索引子的邏輯。 在您可能會執行此程式多次的假設之下，此程式碼會檢查並刪除現有的同名資源。

資料來源物件上會配置專屬於 Azure SQL 資料庫資源的設定，包括[部份或增量索引編製](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)，以充分利用 Azure SQL 的內建[變更偵測功能](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)。 在 Azure SQL 中，示範用的飯店資料庫具有名為 **IsDeleted** 的「虛刪除」資料行。 當此資料行在資料庫中設定為 true 時，索引子就會從 Azure 認知搜尋索引中移除對應文件。

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

索引子物件可跨平台使用，不論來源為何，其中的設定、排程和引動過程都相同。 此索引子範例包括排程及用於清除索引子記錄的重設選項，並且會呼叫方法來立即建立並執行索引子。

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>執行索引子

在此步驟中，編譯並執行程式。 

1. 在 [方案總管] 中，於 **DotNetHowToIndexers** 上按一下滑鼠右鍵，然後選取 [建置]  。
2. 同樣地，以滑鼠右鍵按一下 **DotNetHowToIndexers**，接著按一下 [偵錯]   > [啟動新執行個體]  。

此程式會在偵錯模式中執行。 主控台視窗會報告每項作業的狀態。

  ![SQL 指令碼](./media/search-indexer-tutorial/console-output.png)

您的程式碼會在 Visual Studio 本機執行，並連線到您在 Azure 上的搜尋服務，而搜尋服務會使用連接字串連線到 Azure SQL Database 並擷取資料集。 這麼多項作業會有數個潛在的失敗點，但如果發生錯誤，請先檢查下列：

+ 您提供搜尋服務連線資訊受限於本教學課程中的服務名稱。 如果您輸入了完整 URL，作業會在索引建立時停止，出現連線失敗錯誤。

+ **appsettings.json** 中的資料庫連線資訊。 它應該是從入口網站取得的 ADO.NET 連接字串，已修改成包含您的資料庫適用的使用者名稱和密碼。 使用者帳戶必須具有擷取資料的權限。

+ 資源限制。 回想一下，免費層有 3 個索引、索引子和資料來源的限制。 最大限制的服務無法建立新的物件。

## <a name="search-the-index"></a>搜尋索引 

在 Azure 入口網站的搜尋服務 [概觀] 頁面中，按一下頂端的 [搜尋總管]  ，以提交新索引的一些查詢。

1. 按一下頂端的 [變更索引]  以選取 hotels  索引。

2. 按一下 [搜尋]  按鈕來發出空白搜尋。 

   您的索引中的三個項目會以 JSON 文件形式傳回。 搜尋總管會以 JSON 傳回文件，以便您檢視整個結構。

3. 接下來，輸入搜尋字串：`search=river&$count=true`。 

   此查詢會叫用 `river` 字詞的全文檢索搜尋，而結果會包含相符文件的計數。 在測試包含數千甚至數百萬份文件的大型索引案例時，傳回相符文件的計數很實用。 在此情況下，只有一份文件符合查詢。

4. 最後，輸入搜尋字串，將 JSON 輸出限制為感興趣的欄位：`search=river&$count=true&$select=hotelId, baseRate, description`。 

   查詢回應會縮減為選取的欄位，導致更簡潔的輸出。

## <a name="view-indexer-configuration"></a>檢視索引子組態

所有索引子 (包括您剛才以程式設計方式建立的索引子) 都會列在入口網站中。 您可以開啟索引子定義及檢視其資料來源，或設定重新整理排程，以掌握全新和變更的資料列。

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在您搜尋服務的 [概觀]  頁面上，按一下 [索引]  、[索引子]  和 [資料來源]  的連結。
3. 選取要檢視的個別物件或修改組態設定。

   ![索引子和資料來源圖格](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>清除資源

在完成教學課程後，最快速的清除方式是刪除包含 Azure 認知搜尋服務的資源群組。 您現在可以刪除資源群組，以永久刪除當中所包含的所有項目。 在入口網站中，資源群組名稱位在 Azure 認知搜尋服務的 [概觀] 頁面上。

## <a name="next-steps"></a>後續步驟

您可以將 AI 擴充資料演算法附加至索引子管線。 下一個步驟中，繼續進行下列教學課程。

> [!div class="nextstepaction"]
> [在 Azure Blob 儲存體中為文件編製索引](search-howto-indexing-azure-blob-storage.md)