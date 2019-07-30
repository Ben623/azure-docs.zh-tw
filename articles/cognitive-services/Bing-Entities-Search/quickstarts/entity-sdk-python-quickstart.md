---
title: 快速入門：使用適用於 Python 的 Bing 實體搜尋 SDK 來搜尋實體
titleSuffix: Azure Cognitive Services
description: 使用本快速入門，透過適用於 Python 的 Bing 實體搜尋 SDK 來搜尋實體。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: 0a4a4e3ce6f318a693b8ee760a0d892eaecb2c82
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479010"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>快速入門：使用 Python 的 Bing 實體搜尋 SDK

使用本快速入門，透過適用於 Python 的 Bing 實體搜尋 SDK 開始搜尋實體。 雖然 Bing 實體搜尋具有與大部分程式設計語言相容的 REST API，但此 SDK 可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py) 上找到。

## <a name="prerequisites"></a>必要條件

* Python [2.x 或 3.x](https://www.python.org/)

* [適用於 Python 的 Bing 實體搜尋 SDK](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

建議您使用 Python 虛擬環境。 您可以使用 venv 模組來安裝和初始化虛擬環境。 您可以透過下列方式安裝 virtualenv：

```Console
python -m venv mytestenv
```

透過下列方式安裝 Bing 實體搜尋 SDK：

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入陳述式。 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. 建立訂用帳戶金鑰的變數，並使用該變數建立新的 `CognitiveServicesCredentials` 物件將用戶端具現化。
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>傳送搜尋要求並接收回應

1. 使用 `client.entities.search()` 和搜尋查詢將搜尋要求傳送至 Bing 實體搜尋。 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. 如果傳回實體，請將 `entity_data.entities.value` 轉換為清單，並列印第一個結果。
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../overview.md )