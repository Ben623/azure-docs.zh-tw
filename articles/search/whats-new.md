---
title: 新功能公告
titleSuffix: Azure Cognitive Search
description: 全新和增強功能的公告，包括將 Azure 搜尋服務的服務重新命名為 Azure 認知搜尋。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0ce2884a2382c7dff2bdb90bd92934609675f314
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834380"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 認知搜尋的新功能

了解該服務的新功能。 將此頁面加入書簽，以掌握服務的最新狀態。

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Azure 搜尋服務的新服務名稱

Azure 搜尋服務現在已重新命名為**Azure 認知搜尋**，以反映核心作業中的認知技能和 AI 處理的擴充使用。 雖然認知技能新增了新功能，但使用 AI 是完全選擇性的。 您可以繼續使用不含 AI 的 Azure 認知搜尋，在您于雲端建立和管理的索引中，透過私人、未經處理、以文字為基礎的內容來建立豐富的全文檢索搜尋解決方案。 

API 版本、Nuget 套件、命名空間和端點都不會變更。 您現有的搜尋解決方案不受服務名稱變更的影響。

## <a name="feature-announcements"></a>功能公告

### <a name="january-2020"></a>2020年1月

+ [客戶管理的加密金鑰](search-security-manage-encryption-keys.md)現在已正式推出。 如果您使用 REST，您可以使用 `api-version=2019-05-06`來存取此功能。 對於 managed 程式碼，即使功能不在預覽中，正確的封裝仍然是[.NET SDK 版本 8.0-preview](search-dotnet-sdk-migration-version-9.md) 。 

+ 搜尋服務端點上的*受限制 IP 存取和私人端點（預覽）* 現在已可在**api 版本 = 2019-10-01.txt-preview**中取得。 您可以在[建立或更新](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate)管理 REST API 中，使用新的**IpRule**和**NetworkRuleSet**屬性來設定安全的端點。 如需 API 版本和區域可用性的詳細資訊，請參閱[如何使用管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)。

### <a name="december-2019"></a>2019 年 12 月

+ [建立應用程式（預覽）](search-create-app-portal.md)是入口網站中的新 wizard，可產生可下載的 HTML 檔案。 檔案隨附的內嵌腳本會轉譯作業 "localhost" 樣式的 web 應用程式，並系結至搜尋服務上的索引。 頁面可在 wizard 中設定，而且可以包含搜尋列、結果區域、提要欄位導覽和自動提示查詢支援。 您可以離線修改 HTML，以擴充或自訂工作流程或外觀。

### <a name="november-2019---ignite-conference"></a>2019年11月-Ignite 會議

+ [增量擴充（預覽）](cognitive-search-incremental-indexing-conceptual.md)會將快取和 statefullness 新增至擴充管線，讓您可以處理特定的步驟或階段，而不會遺失已處理的內容。 先前，對擴充管線進行的任何變更都需要完整重建。 使用累加擴充時，會保留昂貴分析的輸出，特別是影像分析。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ 檔[解壓縮（預覽）](cognitive-search-skill-document-extraction.md)是在編制索引期間使用的認知技能，可讓您從技能集中解壓縮檔案的內容。 過去，只有在技能集執行之前才會發生檔破解。 隨著這項技能的增加，您也可以在技能集執行期間執行此作業。

+ [文字翻譯（預覽）](cognitive-search-skill-text-translation.md)是在索引編制期間所使用的認知技能，可評估文字，而針對每一筆記錄，會傳回轉譯為指定目的語言的文字。

+ [Power BI 範本](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)可以快速開始您的視覺效果，並分析 Power BI desktop 中的知識存放區中豐富的內容。 此範本是針對透過 [匯[入資料] wizard](knowledge-store-create-portal.md)建立的 Azure 資料表投影所設計。

+ [Azure Data Lake Storage Gen2 （預覽）](search-howto-index-azure-data-lake-storage.md)、 [Cosmos DB Gremlin API （預覽）](search-howto-index-cosmosdb.md)和[Cosmos DB Cassandra API （預覽）](search-howto-index-cosmosdb.md)現在已在索引子中支援。 您可以使用[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)進行註冊。 一旦您接受預覽計畫，就會收到一封確認電子郵件。

### <a name="july-2019"></a>2019 年 7 月

+ 已在[Azure Government 雲端](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)中正式推出。

## <a name="service-updates"></a>服務更新

Azure 認知搜尋的[服務更新宣告](https://azure.microsoft.com/updates/?product=search&status=all)可在 azure 網站上找到。