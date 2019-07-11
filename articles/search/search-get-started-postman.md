---
title: 快速入門：使用 Postman 和 REST API 建立、載入及查詢索引 - Azure 搜尋服務
description: 了解如何使用 Postman 和範例資料與定義呼叫 Azure 搜尋服務 REST API。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b3c5c08e9862d59481573b501045466d6d691a77
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485534"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>快速入門：使用 REST API 在 Postman 中建立 Azure 搜尋服務索引
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [入口網站](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

探索 [Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice) 的最簡單方式之一，就是使用 Postman 或其他 Web 測試工作來制訂 HTTP 要求及檢查回應。 透過適當的工具與下列指示，您可以在撰寫任何程式碼前，先傳送要求並檢視回應。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)，然後[註冊 Azure 搜尋服務](search-create-service-portal.md)。

## <a name="prerequisites"></a>必要條件

本快速入門會使用下列服務和工具。 

+ [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

+ [Postman 傳統型應用程式](https://www.getpostman.com/)或 [Telerik Fiddler](https://www.telerik.com/fiddler) 會用來將要求傳送至 Azure 搜尋服務。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 搜尋服務是同時建立，因此如果您將 Azure 搜尋服務新增至您的訂用帳戶，請遵循下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]   >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="connect-to-azure-search"></a>連線到 Azure 搜尋服務

在本節中，您可以使用所選 Web 工具來設定與 Azure 搜尋服務的連線。 每個工具都會保存工作階段的要求標頭資訊，這表示您只需要輸入一次 API 金鑰和內容類型。

針對任何一種工具，您都需要選擇命令 (GET、POST 及 PUT等等) 和提供 URL 端點，並且針對某些工作，在要求本文中提供 JSON。 使用有效值取代搜尋服務名稱 (YOUR-SEARCH-SERVICE-NAME)。 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

您會看到 HTTPS 前置詞、服務名稱、物件名稱 (此案例使用索引集合) 和 [API 版本](search-api-versions.md)。 API 版本是針對目前版本指定的必要小寫字串，例如 `?api-version=2019-05-06`。 API 版本會定期更新。 在每個要求上包括 API 版本可讓您具備所使用之版本的完整控制權。  

要求標頭組合包含兩個元素：內容類型及用來對 Azure 搜尋服務進行驗證的 API 金鑰。 使用有效值取代管理員 API 金鑰 (YOUR-ADMIN-API-KEY)。 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

在 Postman 中，可制訂如下列螢幕擷取畫面所示的要求。 選擇 **GET** 作為動詞、提供 URL，然後按一下 [傳送]  。 此命令會連線至 Azure 搜尋服務、讀取索引集合，並在連線成功時傳回 HTTP 狀態碼 200。 如果您的服務已有索引，回應也會包含索引定義。

![Postman 要求標頭][6]

## <a name="1---create-an-index"></a>1 - 建立索引

在 Azure 搜尋服務中，您通常會先建立索引，然後再載入資料。 這項工作會使用[建立索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\)。 

我們已延長 URL 來加入 `hotels` 索引名稱。

若要在 Postman 中執行這項操作：

1. 將動詞變更為 **PUT**。

2. 複製此 URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`。

3. 在要求本文中提供索引定義 (如下所示)。

4. 按一下 [ **傳送**]。

![Postman 要求本文][8]

### <a name="index-definition"></a>索引定義

欄位集合會定義文件結構。 每份文件都必須有這些欄位，而且每個欄位都必須有資料類型。 字串欄位用於全文檢索搜尋，因此如果您需要讓該內容可搜尋，您可能想要將數值資料轉換為字串。

欄位上的屬性會決定允許的動作。 REST API 預設允許許多動作。 例如，預設所有字串都可搜尋、可擷取、可篩選，以及可 Facet。 通常，您只需要在必須關閉行為時設定屬性。

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

當您提交此要求時，您應該會取得 HTTP 201 回應，指出已成功建立索引。 您可以在網站中驗證此動作，但是請注意，入口網站分頁有重新整理間隔，因此可能需要一兩分鐘才會更新。

> [!TIP]
> 如果您收到 HTTP 504，請確認 URL 所指定的是 HTTPS。 如果您看見 HTTP 400 或 404，請查看要求本文以確認並沒有「複製-貼上」錯誤。 HTTP 403 通常表示 API 金鑰有問題 (可能是金鑰無效或是用來指定 API 金鑰的語法有問題)。

## <a name="2---load-documents"></a>2 - 載入文件

建立索引和填入索引是個別的步驟。 在 Azure 搜尋服務中，索引會包含所有可搜尋的資料，您可以提供來作為 JSON 文件。 這項工作會使用[新增、更新或刪除文件 REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。 

我們已延長 URL 來加入 `docs` 集合和 `index` 作業。

若要在 Postman 中執行這項操作：

1. 將動詞變更為 **POST**。

2. 複製此 URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`。

3. 在要求本文中提供 JSON 文件 (如下所示)。

4. 按一下 [ **傳送**]。

![Postman 要求裝載][10]

### <a name="json-documents-to-load-into-the-index"></a>要載入索引的 JSON 文件

[Request Body (要求本文)] 包含 4 個要新增到飯店索引的文件。

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "description_fr": "Hôtel le moins cher en ville",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 200 的回應。 這表示已成功建立文件。 

如果您收到 207，表示至少有一個文件上傳失敗。 如果您收到 404，則表示要求的標頭或本文有語法錯誤：確認您已變更端點以包含 `/docs/index`。

> [!Tip]
> 針對選取的資料來源，您可以選擇替代「索引子」  方法，這個方法可簡化並減少編制索引所需的程式碼數量。 如需詳細資訊，請參閱[索引子作業](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)。


## <a name="3---search-an-index"></a>3 - 搜尋索引

現在已載入索引和文件，您可以使用[搜尋文件 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) \(英文\) 對其發出查詢。

我們已延長 URL 來加入使用搜尋運算子指定的查詢字串。

若要在 Postman 中執行這項操作：

1. 將動詞變更為 **GET**。

2. 複製此 URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`。

3. 按一下 [ **傳送**]。

此查詢會搜尋 "motel" 一詞，並在搜尋結果中傳回文件計數。 在您按一下 [傳送]  之後，Postman 的要求和回應看起來應該類似下列螢幕擷取畫面。 狀態碼應為 200。

 ![Postman 查詢回應][11]

## <a name="get-index-properties"></a>取得索引屬性
您也可以查詢系統資訊以取得文件計數和儲存體用量：`https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

在 Postman 中，您的要求看起來應該與下方項目類似，回應會包括文件計數和使用的空格 (以位元組表示)。

 ![Postman 系統查詢][12]

請注意，API 版本語法不同。 針對此要求，使用 `?` 以附加 API 版本。 從查詢字串分隔 URL 路徑，`?` 會分隔查詢字串中的每個 'name=value' 組。 針對此查詢，API 版本是查詢字串中第一個也是唯一一個項目。

如需此 API 的詳細資訊，請參閱[取得索引統計資料 REST API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) \(英文\)。

## <a name="clean-up"></a>清除

如果您不再需要搜尋服務，釋出服務最快速的方式是刪除包含 Azure 搜尋服務的資源群組。 永久刪除資源群組會一併刪除其中所有項目，包括服務和任何儲存的內容。 在入口網站中，資源群組名稱位在每個服務的 [概觀] 頁面上。

## <a name="next-steps"></a>後續步驟

REST 用戶端對於臨時探索相當寶貴，但是現在您知道 REST API 的運作方式，您可以繼續進行程式碼。 對於您的下一個步驟，請參閱下列連結：

+ [快速入門：使用 .NET SDK 建立索引](search-create-index-dotnet.md)
+ [快速入門：使用 PowerShell 建立索引 (REST)](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-get-started-postman/fiddler-url.png
[2]: ./media/search-get-started-postman/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-get-started-postman/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-get-started-postman/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-get-started-postman/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-get-started-postman/postman-url.png
[7]: ./media/search-get-started-postman/fiddler-request.png
[8]: ./media/search-get-started-postman/postman-request.png
[9]: ./media/search-get-started-postman/fiddler-docs.png
[10]: ./media/search-get-started-postman/postman-docs.png
[11]: ./media/search-get-started-postman/postman-query.png
[12]: ./media/search-get-started-postman/postman-system-query.png
