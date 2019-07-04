---
title: Bing 搜尋 API 是什麼？
titleSuffix: Azure Cognitive Services
description: 您可以使用本文來了解 Bing 搜尋 API，以及如何啟用應用程式及服務的認知網際網路搜尋。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61431356"
---
# <a name="what-are-the-bing-search-apis"></a>Bing 搜尋 API 是什麼？

Bing 搜尋 Api 可讓您建立 web 連接的應用程式及尋找網頁、 影像、 新聞、 位置和沒有廣告的更多的服務。 藉由使用 Bing 搜尋 REST API 或 SDK 傳送搜尋要求，您可以取得網路搜尋的相關資訊和內容。 您可以使用本文來了解不同的 Bing 搜尋 Api 以及將 infocards 整合認知搜尋您的應用程式和服務。 API 之間可能有不同的定價與速率限制。

## <a name="the-bing-web-search-api"></a>Bing Web 搜尋 API

[Bing Web 搜尋 API](../Bing-Web-Search/index.yml) 會傳回網頁、影像、影片、新聞等等。 您可以篩選傳送到此 API，以包含或排除特定內容類型的搜尋查詢。

請考慮在可能需要搜尋各種相關網路內容的應用程式中使用 Bing Web 搜尋 API。 如果您的應用程式搜尋特定類型的線上內容，請考慮下列其中一個搜尋 API：

## <a name="content-specific-bing-search-apis"></a>特定內容的 Bing 搜尋 API

下列 Bing 搜尋 Api 會傳回從映像、 新聞、 當地商業和影片等 web 特定內容。

| Bing API | 描述 |
| -- | -- |
| [實體搜尋](../Bing-Entities-Search/index.yml) | Bing 實體搜尋 API 會傳回內含人員、地點或事物等等實體的搜尋結果。 依查詢，這個 API 會傳回符合搜尋查詢的一或多個實體。 值得注意的個人、 當地商業，地標、 目的地和更多功能，可以包含搜尋查詢。 |
| [影像搜尋](../Bing-Image-Search/index.yml) | Bing 影像搜尋 API 可讓您搜尋和尋找高品質靜態和動畫映像類似於[Bing.com/images](https://www.Bing.com/images)。 您可以精簡搜尋來依屬性包含或排除影像，包括大小、色彩、授權和有效期限。 您也可以搜尋趨勢影像、上傳影像以深入了解影像，以及顯示縮圖預覽。 |
| [新聞搜尋](../Bing-News-Search/index.yml) | Bing 新聞搜尋 API 可讓您尋找類似的新聞報導[Bing.com/news](https://www.Bing.com/news)。 此 API 會傳回多個來源或特定網域的新聞文章。 您可以跨類別搜尋，以取得趨勢文章、熱門文章和頭條新聞。 |
| [影片搜尋](../Bing-Video-Search/index.yml) | Bing 影片搜尋 API 可讓您在網路上尋找影片。 取得發燒影片、相關內容和縮圖預覽。 |
| [圖像式搜尋](../Bing-visual-search/index.yml) | 上傳影像，或使用網址取得相關的深入資訊，例如看起來類似的產品、影像和相關搜尋。 |
 [本地商家搜尋](../bing-local-business-search/index.yml) | Bing 本機企業搜尋 API 可讓您尋找根據搜尋查詢的當地商業的連絡人和位置資訊的應用程式。 |

## <a name="the-bing-custom-search-api"></a>Bing 自訂搜尋 API

建立自訂搜尋執行個體與[Bing 自訂搜尋](../Bing-Custom-Search/index.yml)API 可讓您建立只著重於內容和您想知道之主題的搜尋體驗。 比方說，在指定的網域、 網站和 Bing 會搜尋的特定網頁之後，Bing 自訂搜尋將會調整該特定內容的結果。 您可以整合 Bing 自訂自動建議、影像和視訊搜尋 API，進一步自訂您的搜尋體驗。

## <a name="additional-bing-search-apis"></a>其他 Bing 搜尋 API

下列 Bing 搜尋 Api 可讓您將它們與其他的 Bing 搜尋 Api 改善搜尋體驗。

| API | 描述 |
| -- | -- |
| [Bing 自動建議](../Bing-Autosuggest/index.yml) | 藉由即時傳回建議的搜尋改善您的應用程式的 Bing 自動建議 api 的搜尋體驗。  |
| [Bing 統計資料](bing-web-stats.md) | Bing 統計資料可為應用程式使用的 Bing 搜尋 API 提供分析。 可用的分析包括呼叫量、熱門查詢字串及地理分佈。 |

## <a name="next-steps"></a>後續步驟

* Bing 搜尋 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Bing 使用和顯示需求](./use-display-requirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。
