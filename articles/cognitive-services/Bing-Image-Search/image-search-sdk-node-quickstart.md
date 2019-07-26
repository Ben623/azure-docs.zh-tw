---
title: 快速入門：搜尋影像 - 適用於 Node.js 的 Bing 影像搜尋 SDK
titleSuffix: Azure Cognitive Services
description: Bing 影像搜尋 SDK 是 API 的包裝函式，而且功能相同，您可以透過此快速入門，完成初次使用此 SDK 進行的影像搜尋。 這個簡單的 Node.js 應用程式會傳送影像搜尋查詢、剖析 JSON 回應，以及顯示第一個回傳影像的 URL。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 24f6de759f233b82cb19a21401d3886a80bd746f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423900"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-nodejs"></a>快速入門：使用適用於 Node.js 的 Bing 影像搜尋 SDK 來搜尋影像

Bing 影像搜尋 SDK 是 API 的包裝函式，而且功能相同，您可以透過此快速入門，完成初次使用此 SDK 進行的影像搜尋。 這個簡單的 JavaScript 應用程式會傳送影像搜尋查詢、剖析 JSON 回應，以及顯示第一個回傳影像的 URL。

此範例的原始程式碼可從 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) 取得，其中還有其他錯誤處理和註釋。

## <a name="prerequisites"></a>必要條件
在「搜尋」  下取得[認知服務存取金鑰](https://azure.microsoft.com/try/cognitive-services/)。  另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。  另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

* [適用於 Node.js 的認知服務影像搜尋 SDK](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * 使用 `npm install azure-cognitiveservices-imagesearch` 進行安裝
* [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure) 模組
    * 使用 `npm install ms-rest-azure` 進行安裝

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立並初始化應用程式

1. 在您喜愛的 IDE 或編輯器中建立新的 JavaScript 檔案，並設定嚴謹度、https 和其他需求。

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    ```

2. 在您專案的主要方法中，針對有效訂用帳戶金鑰、要讓 Bing 傳回的影像結果和搜尋字詞建立變數。 然後使用金鑰具現化影像搜尋用戶端。

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client 
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>建立非同步的協助程式函式

1. 建立函式以非同步方式呼叫用戶端，並從 Bing 影像搜尋服務傳回回應。  
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>傳送查詢並處理回應

1. 呼叫協助程式函式並處理其 `promise`，以剖析回應中傳回的影像結果。

    如果回應包含搜尋結果，請儲存第一個結果並列印出其詳細資料，例如縮圖 URL、原始 URL 及傳回影像的總數。  
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 影像搜尋單頁應用程式教學課程](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) (英文)

## <a name="see-also"></a>另請參閱

* [什麼是 Bing 影像搜尋？](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [試用線上互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [取得免費認知服務存取金鑰](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Azure 認知服務 SDK 的 Node.js 範例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Azure 認知服務文件](https://docs.microsoft.com/azure/cognitive-services)
* [Bing 影像搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
