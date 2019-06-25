---
title: Azure 地圖服務概觀 | Microsoft Docs
description: Azure 地圖服務簡介
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 8092cd169f93a6815e52517d805941ac7ddcbbc0
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807506"
---
# <a name="what-is-azure-maps"></a>什麼是 Azure 地圖服務？

Azure 地圖服務是地理空間服務的集合，以最新的可用地圖資料作為後盾，進而為 Web 和行動裝置應用程式提供準確的地理內容。 Azure 地圖服務是由 REST API 組成，可用於呈現多種樣式的**地圖**和衛星影像、**搜尋**地址、地點及全球景點；點對點和多點**路線規劃**、多點最佳化、等時線、營業用車、受影響的交通，以及矩陣路線規劃；領先業界的交通流量和事故檢視；要求公共運輸、自行車共享、機車共享和汽車共享資訊的**行動**服務，以運用替代交通模式和即時資料來規劃路線；透過 [地理位置]  建立使用者位置，以及將位置轉換為 [時區]  ，也可擷取某個位置的時間。 此外，Azure 地圖服務可為 [地理柵欄]  、[地圖資料]  儲存體 (在 Azure 中裝載位置資訊) 及 [空間作業]  提供服務，進而透過地理空間分析提供定位智慧。 Azure 地圖服務可直接作為 REST API 使用，或透過我們強固的 **Web SDK** 或 **Android SDK** 提供。 這些工具可讓開發人員快速開發和調整解決方案，以將位置資訊整合到 Azure 雲端的 Azure 解決方案中。 立即註冊免費的 [Azure 地圖服務帳戶](https://azure.microsoft.com/services/azure-maps/)，開始進行開發！

以下影片會深入說明 Azure 地圖服務：

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>地圖控制項

### <a name="web-sdk"></a>Web SDK

Azure 地圖服務 Web SDK 可讓您以自己的內容和圖像，自訂顯示在 Web 或行動應用程式中的互動式地圖。 此控制項使用 WebGL，可讓您以高效能轉譯大型資料集。 您可以使用 JavaScript 或 TypeScript 以 SDK 進行開發。

![Azure 地圖服務 Web SDK](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Azure 地圖服務 Android SDK 可讓您建立功能強大的行動地圖應用程式。 

![Azure 地圖服務 Android SDK](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Azure 地圖服務中的服務

Azure 地圖服務是由下列九個服務組成，可以為 Azure 應用程式提供地理內容。

### <a name="data-service"></a>資料服務

地圖中的資料極為重要，而讓客戶資料更貼近 Azure 地圖服務可減少延遲、增加生產力及建立強大、全新的案例，以在您的應用程式中突顯出來。 資料服務可讓您上傳並儲存用於空間作業或影像編譯的地理空間資料，以在應用程式中縮短延遲、提升生產力，及實踐新的案例。 如需這項服務的詳細資訊，請瀏覽[資料服務 API](https://docs.microsoft.com/rest/api/maps/data) 頁面。

### <a name="mobility-service"></a>行動服務

Azure 地圖行動服務提供附近公共運輸服務的即時定位智慧，包括停靠站、路線資訊及預估旅程時間。 此服務允許搜尋特定物件類型，例如指定地點附近的公共運輸站牌、共享自行車/機車/騎車，並傳回一組運輸物件與物件詳細資料。 此服務讓開發人員也可要求運輸路線詳細資料，其中涵蓋基本資訊和其他詳細資料，例如路線幾何、站牌清單、排定和即時的運輸到達和服務警示。 使用者也可以藉由要求租借站資訊，了解最近的租借站目前有多少輛可用的共享自行車。 行動服務也能夠搜尋可用的汽車共享車輛，並傳回未來可用性和目前燃料存量等詳細資料。
Azure 地圖行動服務允許即時路線規劃、傳回可能最佳的路線選項，以及提供各種行進模式，包括步行、騎自行車和捷運區域 (市區) 內可用的公共運輸。 此外，開發人員可以要求運輸行程詳細資料，以及路線幾何和詳細行程排程等額外資訊。

若要深入了解服務以及各種功能，請參閱我們的 [API 文件](https://docs.microsoft.com/rest/api/maps/mobility)。

### <a name="render-service"></a>轉譯服務

轉譯服務是針對開發人員設計的服務，可建立以地圖服務為主的 Web 和行動裝置應用程式。 此服務會使用高品質點陣圖形影像 (提供 19 個縮放層級)，或可完全自訂的向量格式地圖影像。

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

轉譯服務現在提供具預覽功能的 API，可讓開發人員使用衛星影像。 如需詳細資訊，請參閱 [Azure 地圖服務轉譯 API](https://docs.microsoft.com/rest/api/maps/render)。

### <a name="route-service"></a>路線規劃服務

路線規劃服務包含真實世界基礎結構的強固幾何計算，與多種交通模式的路線。 服務可讓開發人員跨幾種旅行模式 (例如汽車、貨車、自行車或步行) 計算方向。 服務也可以考量輸入，例如交通情況、重量限制，或危險材料運輸。

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

路線規劃服務現在提供進階功能預覽，例如批次處理多個路線要求、起點與目的地之間移動時間和距離的對照表，並且根據您的時間或燃料需求，尋找可以移動的路線或距離。 如需路線規劃功能的詳細資訊，請參閱 [Azure 地圖服務路線規劃 API](https://docs.microsoft.com/rest/api/maps/route)。

### <a name="search-service"></a>搜尋服務

搜尋服務是針對開發人員而設計的服務，可搜尋地址、位置、依名稱或類別的企業清單，以及其他地理資訊。 搜尋服務也可以根據經度和緯度進行地址與交叉路口的[反向地理編碼](https://en.wikipedia.org/wiki/Reverse_geocoding)。

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

搜尋服務也提供進階功能，例如沿路線搜尋、在較廣泛的區域內搜尋、批次處理一組搜尋要求，以及搜尋較大的區域而不是位置點。 批次處理和區域搜尋的 API 目前處於預覽狀態。 如需搜尋功能的詳細資訊，請參閱 [Azure 地圖服務搜尋 API](https://docs.microsoft.com/rest/api/maps/search) 頁面。

### <a name="spatial-operations"></a>空間作業

Azure 地圖服務空間作業會取得位置資訊並進行即時分析，協助通知客戶有關某個時間和空間發生的進行中事件、進行事件幾近即時的分析和預測性模型化。 服務能夠讓 Azure 地圖服務的客戶以原生方式透過內含常見地理空間數學計算 (包括最接近點、大圓距離及環域等服務) 的程式庫，增強其定位智慧。 若要深入了解服務以及各種功能，請參閱我們的 [API 文件](https://docs.microsoft.com/rest/api/maps/spatial)。

### <a name="time-zone-service"></a>時區服務

時區服務可讓您使用其中經緯度或 [IANA 識別碼](https://www.iana.org/)，查詢目前、過去及未來的時區資訊。 時區服務也可將 Microsoft Windows 時區識別碼轉換為 IANA 時區、擷取 UTC 的時區位移，以及取得各自時區中的目前時間。 時區服務查詢的典型 JSON 回應如以下範例所示：

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

如需這項服務的詳細資訊，請瀏覽 [Azure 地圖服務時區 API](https://docs.microsoft.com/rest/api/maps/timezone) 頁面。

### <a name="traffic-service"></a>交通服務

交通服務是針對開發人員設計的一套 Web 服務，可建立需要路況的 Web 和行動裝置應用程式。 服務可提供兩種資料類型：

* 交通流量 - 針對路網中的所有重要道路提供即時觀察速度和行進時間。
* 交通事故 - 提供有關路網中交通阻塞和交通事故的最新檢視。

![Azure 地圖服務流量](media/about-azure-maps/Introduction_Traffic.png)

如需詳細資訊，請瀏覽 [Azure 地圖服務交通 API](https://docs.microsoft.com/rest/api/maps/traffic) 頁面。

### <a name="ip-to-location"></a>IP 到地點

「IP 到地點」服務可讓您預覽針對給定 IP 位址擷取的兩字元國家代碼。 此服務可協助您透過根據地理位置自訂應用程式內容，來量身訂做並加強使用者體驗。

如需與「IP 到地點」服務的 REST API 有關的資訊，請瀏覽 [Azure 地圖服務地理位置 API](https://docs.microsoft.com/rest/api/maps/geolocation) 頁面。

## <a name="programming-model"></a>程式設計模型

Azure 地圖服務是針對行動性而建置，可以為跨平台的應用程式提供服務。 它使用與語言無關的程式設計模型，並且透過 [REST API](https://docs.microsoft.com/rest/api/maps/) 支援 JSON 輸出。

此外，Azure 地圖服務使用簡單的程式設計模型提供方便的 [JavaScript 地圖控制項](https://docs.microsoft.com/javascript/api/azure-maps-control)，以便快速輕鬆開發 Web 和行動裝置應用程式。

## <a name="usage"></a>使用量

存取地圖服務時須瀏覽至 [Azure 入口網站](https://portal.azure.com)，並建立 Azure 地圖服務帳戶。

Azure 地圖服務會使用金鑰型驗證結構描述。 您的帳戶隨附預先為您產生的兩個金鑰。 透過使用任一金鑰並建立對 Azure Maps 服務的要求，以從整合這些位置功能到您的應用程式開始。

## <a name="supported-regions"></a>支援區域

Azure 地圖服務 API 目前可在下列區域以外的所有國家/地區中使用：

* 阿根廷
* 中國
* 印度
* 摩洛哥
* 巴基斯坦
* 南韓

確認您目前 IP 位址的位置不在上述未受支援的國家/地區中。

## <a name="next-steps"></a>後續步驟

如需 Azure 地圖服務新功能的詳細資訊：

> [!div class="nextstepaction"]
> [路線對照表、等時路線規劃、IP 查閱等等](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

立即試用能展現 Azure 地圖服務優勢的範例應用程式：

> [!div class="nextstepaction"]
> [快速入門：建立 Web 應用程式](quick-demo-map-app.md)
