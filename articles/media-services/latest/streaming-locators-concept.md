---
title: Azure 媒體服務中的串流定位器 | Microsoft Docs
description: 本文解釋串流定位器是什麼，以及 Azure 媒體服務用它們來做什麼。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 6d13ca5b3657f1deac9e6b4218decf8fe57eb1d9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113749"
---
# <a name="streaming-locators"></a>串流定位器

若要讓輸出資產中的影片可供用戶端播放，您必須建立[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)，然後建置串流 URL。 若要建立 URL，您需要串連串流端點主機名稱和串流定位器路徑。 如需 .NET 範例，請參閱[取得串流定位器](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

建立 [串流定位器] 的程序稱為發佈。 根據預設，[串流定位器] 會在進行 API 呼叫後立即生效，而且會持續運作到遭到刪除為止 (除非您有設定選擇性的開始和結束時間)。 

建立**串流定位器**時，您必須指定**資產**名稱和**串流原則**名稱。 如需詳細資訊，請參閱下列主題：

* [資產](assets-concept.md)
* [串流原則](streaming-policy-concept.md)
* [內容金鑰原則](content-key-policy-concept.md)

您也可以指定串流定位器的開始和結束時間，這只會讓您的使用者在這些時間之間播放內容（例如，介於5/1/2019 到5/5/2019 之間）。  

## <a name="considerations"></a>注意事項

* **串流定位器**不是可更新的。 
* 屬於日期時間類型的**串流定位器**屬性一律為 UTC 格式。
* 您應該為媒體服務帳戶設計一組受限的原則，並且在需要相同的選項時，對串流定位器重新使用這些原則。 如需詳細資訊，請參閱 [配額和限制](limits-quotas-constraints.md)。

## <a name="create-streaming-locators"></a>建立串流定位器  

### <a name="not-encrypted"></a>未加密

如果您想要以純文字（未加密）串流處理您的檔案，請將預先定義的清除串流原則設定為 ' Predefined_ClearStreamingOnly ' （在 .NET 中，您可以使用 Predefinedstreamingpolicy.clearstreamingonly. Predefinedstreamingpolicy.clearstreamingonly 列舉）。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>已加密 

如果您需要使用 CENC 加密來加密您的內容，請將您的原則設定為 ' Predefined_MultiDrmCencStreaming '。 Widevine 加密會套用至破折號串流，PlayReady 則會順暢地進行。 金鑰會根據所設定的 DRM 授權傳遞至播放用戶端。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

如果您也想要使用 CBCS （FairPlay）來加密 HLS 串流，請使用 ' Predefined_MultiDrmStreaming '。

## <a name="associate-filters-with-streaming-locators"></a>將篩選器與串流定位器建立關聯

請參閱[篩選：與串流定位器產生關聯](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="filter-order-page-streaming-locator-entities"></a>篩選、排序、分頁串流定位器實體

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="list-streaming-locators-by-asset-name"></a>依資產名稱列出串流定位器

若要根據相關聯的資產名稱取得串流定位器，請使用下列作業：

|語言|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams 資產清單-串流-定位器](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="also-see"></a>另請參閱

* [資產](assets-concept.md)
* [串流原則](streaming-policy-concept.md)
* [內容金鑰原則](content-key-policy-concept.md)

## <a name="next-steps"></a>後續步驟

[教學課程：使用 .NET 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
