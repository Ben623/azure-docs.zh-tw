---
title: 使用文字分析 REST API 進行關鍵片語擷取 | Microsoft Docs
description: 如何使用 Azure 認知服務中的文字分析 REST API 來擷取關鍵片語。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 06/05/2019
ms.author: raymondl
ms.openlocfilehash: c803c85a0900a09b18909e2c81d52915a12cff1a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304064"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>範例：如何使用文字分析來擷取關鍵片語

[關鍵片語擷取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) \(英文\) 會評估非結構化的文字，並針對每份 JSON 文件，傳回關鍵片語的清單。 

此功能在您需要快速識別文件集合中的要點時相當有用。 例如，假設輸入文字為 "The food was delicious and there were wonderful staff"，服務即會傳回主要討論要點："food" 和 "wonderful staff"。

如需詳細資訊，請參閱[支援的語言](../text-analytics-supported-languages.md)一文。 

> [!TIP]
> 文字分析也會提供可用來擷取關鍵片語的 Linux 型 Docker 容器映像，好讓您可以在接近資料的位置[安裝和執行文字分析容器](text-analytics-how-to-install-containers.md)。

## <a name="preparation"></a>準備工作

關鍵片語擷取在處理較大量文字時的效果最佳。 這與情感分析相反，後者較適合用來處理較少量的文字。 若要從這兩個作業中取得最佳結果，請考慮據以重建輸入。

您必須具有此格式的 JSON 文件：識別碼、文字、語言

文件大小必須少於 5,120 個字元，而且您最多可以針對每個集合擁有 1,000 個項目 (識別碼)。 集合會在要求本文中提交。 下列範例是您可能會針對關鍵片語擷取所提交內容的說明。

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>步驟 1：建立要求結構

關於要求定義的詳細資料可以在[如何呼叫文字分析 API](text-analytics-how-to-call-api.md) 中找到。 為了方便起見，我們將重申下列各點：

+ 建立一個 **POST** 要求。 檢閱適用於此要求的 API 文件：[關鍵片語 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)

+ 使用 Azure 文字分析資源或具現化的[文字分析容器](text-analytics-how-to-install-containers.md)，來設定可用來擷取關鍵片語的 HTTP 端點。 它必須包括 `/keyPhrases` 資源：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`

+ 設定要求標頭以包含適用於文字分析作業的存取金鑰。 如需詳細資訊，請參閱[如何尋找端點和存取金鑰](text-analytics-how-to-access-key.md)。

+ 在要求本文中，提供您準備用於此分析的 JSON 文件集合

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟[文件](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) \(英文\) 中的 **API 測試主控台**來建立要求結構，並將它 POST 到服務。

## <a name="step-2-post-the-request"></a>步驟 2：張貼要求

分析會在接收要求時執行。 請參閱概觀中的[資料限制](../overview.md#data-limits)一節，以取得您每分鐘和每秒鐘可以傳送的要求大小和數量資訊。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。

## <a name="step-3-view-results"></a>步驟 3：檢視結果

所有的 POST 要求都會傳回 JSON 格式的回應，具有識別碼和偵測到的屬性。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。

以下顯示關鍵片語擷取的輸出範例：

```json
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

如前所述，分析器會尋找並捨棄非必要的單字，並保留系統判斷為句子的主詞或受詞的單一詞彙或片語。 

## <a name="summary"></a>總結

在本文中，您已了解使用認知服務中的文字分析進行關鍵片語擷取的概念和工作流程。 摘要說明：

+ [關鍵片語擷取 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) \(英文\) 僅針對特定語言提供。
+ 要求主體中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](text-analytics-how-to-access-key.md)，將要求 POST 到 `/keyphrases` 端點。
+ 回應輸出 (其包含針對每個文件識別碼的關鍵單字和片語) 可以串流處理到任何可接受 JSON 的應用程式，包括 Excel 和 Power BI 等。

## <a name="see-also"></a>另請參閱 

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
