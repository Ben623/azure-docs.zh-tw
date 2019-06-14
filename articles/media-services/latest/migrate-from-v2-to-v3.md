---
title: 從 Azure 媒體服務 v2 遷移至 v3 | Microsoft Docs
description: 本文說明 Azure 媒體服務 v3 中所導入的變更，並說明兩個版本之間的差異。 該文章也提供從媒體服務 v2 移至 v3 的移轉指導。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure 媒體服務, 串流, 廣播, 即時, 離線
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: da8d1000ebe2695dbc95d475027722962a3305d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65555885"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>從媒體服務 v2 移動至 v3 的移轉指導

本文說明 Azure 媒體服務 v3 中所導入的變更、說明兩個版本之間的差異，並提供移轉指導。

如果您現今已經在[舊版媒體服務 v2 API](../previous/media-services-overview.md) 上開發影片服務，您應該先檢閱下列指導方針和考量，然後再移轉至 v3 API。 v3 API 中有許多優點和新功能，可改善媒體服務的開發人員經驗和功能。 不過，如本文[已知問題](#known-issues)一節中所提出，由於 API 版本之間的變更，因此也有一些限制。 此頁面將經過維護，因為媒體服務團隊會一直持續不斷地改進 v3 API，並解決版本之間的差距。 

> [!NOTE]
> 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

## <a name="benefits-of-media-services-v3"></a>媒體服務 v3 的優點
  
### <a name="api-is-more-approachable"></a>API 會更平易近人

*  v3 是會統一的 API 表面為基礎，併公開建置於 Azure Resource Manager 上的管理和操作功能。 Azure Resource Manager 範本可用於建立及部署轉換、串流端點、即時事件等等。
* [OpenAPI 規格 （先前稱為 Swagger）](https://aka.ms/ams-v3-rest-sdk)文件。
    公開所有服務元件的結構描述，包括檔案型編碼。
* SDK 適用於 [.NET](https://aka.ms/ams-v3-dotnet-ref)、.Net Core、[Node.js](https://aka.ms/ams-v3-nodejs-ref)、[Python](https://aka.ms/ams-v3-python-ref)、[Java](https://aka.ms/ams-v3-java-ref)、[Go](https://aka.ms/ams-v3-go-ref) 和 Ruby。
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) 整合，適用於簡單的指令碼支援。

### <a name="new-features"></a>新功能

* 對於檔案型作業處理，您可以使用 HTTP(S) URL 作為輸入。<br/>您不需要已經將內容儲存在 Azure 中，也不需要建立 Asset。
* 介紹檔案型作業處理的 [Transform](transforms-jobs-concept.md) 概念。 Transform 可用來建置可重複使用的組態、建立 Azure Resource Manager 範本，並隔離多個客戶或租用戶之間的處理設定。
* 一個 Asset 可以有多個[串流定位器](streaming-locators-concept.md)，各自具有不同的[動態封裝](dynamic-packaging-overview.md)與「動態加密」設定。
* [內容保護](content-key-policy-concept.md)可支援多索引鍵功能。
* 使用媒體服務將單一位元速率貢獻饋送轉碼為有多個位元速率的輸出資料流時，您可以串流處理長達 24 小時的即時事件。
* 即時事件上新的低延遲即時串流處理支援。 如需詳細資訊，請參閱[延遲](live-event-latency.md)。
* 即時事件預覽版可支援[動態封裝](dynamic-packaging-overview.md)與「動態加密」。 這可在預覽版上實現內容保護，以及 DASH 和 HLS 封裝。
* 即時輸出在使用上比 v2 API 中的 Program 實體更簡單。 
* 改進的 RTMP 支援 (更高的穩定性及更多來源編碼器支援)。
* RTMPS 安全內嵌。<br/>當您建立即時事件時，您會取得 4 個內嵌 URL。 4 個內嵌 URL 幾乎完全相同，並有相同的串流權杖 (AppId)，只有連接埠號碼部分不同。 其中兩個 URL 是 RTMPS 的主要部分和備份。   
* 您對實體已經有角色型存取控制 (RBAC)。 

## <a name="changes-from-v2"></a>從 v2 的變更

* 對於以 v3 建立的 Asset，媒體服務僅支援 [Azure 儲存體伺服器端儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。
    * 您可以使用 v3 API 搭配以 v2 API 建立的 Asset，後者的[儲存體加密](../previous/media-services-rest-storage-encryption.md) (AES 256) 是由媒體服務所提供。
    * 您無法使用 v3 API 建立具有舊版 AES 256 [儲存體加密](../previous/media-services-rest-storage-encryption.md)的新 Asset。
* v3 SDK 現在已與儲存體 SDK 分離，其掌控力勝過所使用的儲存體 SDK，並可避免版本控制問題。 
* 在 v3 API 中，所有的編碼位元速率單位都是「位元/秒」。 這不同於 v2 媒體編碼器標準預設。 例如，v2 中的位元速率會指定為 128 (kbps)，但在 v3 中，則會是 128000 (位元/秒)。 
* 實體 AssetFiles、AccessPolicies 和 IngestManifests 不存在於 v3 中。
* IAsset.ParentAssets 屬性不在 v3 中。
* ContentKey 不再是實體，現在是串流定位器的屬性。
* 事件方格支援取代了 NotificationEndpoint。
* 下列實體已重新命名
    * 作業輸出會取代工作，而且現在是作業的一部分。
    * 串流定位器取代了定位器。
    * 即時事件取代了通道。<br/>即時事件計費是以直播頻道計量為基礎。 如需詳細資訊，請參閱[計費](live-event-states-billing.md)和[定價](https://azure.microsoft.com/pricing/details/media-services/)。
    * 即時輸出取代了程式。
* 「實況輸出」會在建立時開始，並在刪除時結束。 程式在 v2 API 中是以不同的方式運作，因此它們必須在建立後啟動。
*  若要取得工作的相關資訊，您需要知道在其建立作業轉換名稱。 

## <a name="feature-gaps-with-respect-to-v2-apis"></a>與 v2 API 相關的功能差距

v3 API 與 v2 API 具有下列功能差距。 縮小差距是刻不容緩的工作。

* [進階編碼器](../previous/media-services-premium-workflow-encoder-formats.md)和舊版[媒體分析處理器](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview、Face Redactor 等) 無法透過 v3 存取。<br/>想要從 Media Indexer 1 或 2 Preview 移轉的客戶可以立即使用 v3 API 中的 AudioAnalyzer 預設值。  相較於舊版的 Media Indexer 1 或 2，這個新的預設值包含更多功能。 
* 許多[媒體編碼器標準 v2 中的進階功能](../previous/media-services-advanced-encoding-with-mes.md)Api 目前不提供的 v3，例如：
    * 裁剪 (適用於隨選和即時的案例)
    * 資產拼接
    * 重疊
    * 裁剪
    * 縮圖原件
    * 插入靜音曲目時輸入具有不具音訊
    * 插入視訊播放軌，當輸入具有不含視訊
* 具有轉碼的即時事件目前不支援候選影片插入中間串流，以及透過 API 呼叫插入廣告標記。 

> [!NOTE]
> 請將本文加入書籤，並持續檢查更新。
 
## <a name="code-differences"></a>程式碼差異

下表顯示 v2 和 v3 常見案例的程式碼差異。

|案例|V2 API|V3 API|
|---|---|---|
|建立資產並上傳檔案 |[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|提交作業|[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>示範如何先建立 Transform，然後再提交 Job。|
|使用 AES 加密發行資產 |1.建立 ContentKeyAuthorizationPolicyOption<br/>2.建立 ContentKeyAuthorizationPolicy<br/>3.建立 AssetDeliveryPolicy<br/>4.建立資產並上傳內容，或提交作業並使用輸出資產<br/>5.讓 AssetDeliveryPolicy 與資產產生關聯<br/>6.建立 ContentKey<br/>7.將 ContentKey 連結至資產<br/>8.建立 AccessPolicy<br/>9.建立定位器<br/><br/>[v2 .NET 範例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1.建立內容金鑰原則<br/>2.建立資產<br/>3.上傳內容或將資產作為 JobOutput<br/>4.建立串流定位器<br/><br/>[v3 .NET 範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|取得工作詳細資料和管理工作 |[管理工作使用 v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[管理工作使用 v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>已知問題

* 目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-sdk)、CLI 或其中一個支援的 SDK。
* 您需要在您的帳戶中佈建媒體保留單位 (MRU)，才能控制作業的並行和效能，尤其是與視訊或音訊分析相關的作業。 如需詳細資訊，請參閱[調整媒體處理](../previous/media-services-scale-media-processing-overview.md)。 您可以使用[適用於媒體服務 v3 的 CLI 2.0](media-reserved-units-cli-how-to.md)、使用 [Azure 入口網站](../previous/media-services-portal-scale-media-processing.md)，或使用 [v2 API](../previous/media-services-dotnet-encoding-units.md)。 不論您是使用媒體服務 v2 或 v3 API，都需要佈建 MRU。
* 使用 v3 API 建立的媒體服務實體無法由 v2 API 管理。  
* 不建議透過 v3 API 管理使用 v2 API 建立的實體。 以下是讓兩個版本的實體不相容的差異範例：   
    * 在 v2 中建立的 Job 和 Task 不會出現在 v3 中，因為它們與 Transform 沒有關聯。 建議是切換至 v3 Transform 和 Job。 在轉換期間將會有一段相當短的時間需要監視傳遞 v2 Job。
    * 使用 v2 建立的通道和程式 (這會對應到 v3 中的即時事件和即時輸出) 無法繼續使用 v3 管理。 建議在方便的通道停止時，切換至 v3 即時事件和即時輸出。<br/>目前您無法移轉持續執行的通道。  

> [!NOTE]
> 此頁面將經過維護，因為媒體服務團隊會一直持續不斷地改進 v3 API，並解決版本之間的差距。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

若要了解開始撰寫程式碼並串流視訊檔案是多麼容易，請查看[串流檔案](stream-files-dotnet-quickstart.md)。 

