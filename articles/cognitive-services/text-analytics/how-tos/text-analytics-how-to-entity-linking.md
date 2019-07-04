---
title: 搭配文字分析 API 使用實體辨識
titleSuffix: Azure Cognitive Services
description: 了解如何使用文字分析 REST API 辨識實體。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: ff4f9af82024e9d39ad89a39bcb2fe4130de9101
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304187"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在 文字分析中使用具名實體辨識

[具名實體辨識 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)會採用非結構化的文字，並針對每個 JSON 文件中，會傳回一份變得更為明確連結的實體的詳細資訊 （維基百科和 Bing） 網站上。 

## <a name="entity-linking-and-named-entity-recognition"></a>實體連結和具名實體辨識

文字分析 '`entities`端點支援具名實體辨識 (NER) 和連結的實體。

### <a name="entity-linking"></a>實體連結
連結實體可識別及區分文字中找到的實體身分識別 (例如，判斷 "Mars" 是用來指星體或指羅馬的戰神)。 此程序需具備可讓辨識項目與之連結的知識庫 - `entities` 端點文字分析即是使用維基百科作為知識庫。

### <a name="named-entity-recognition-ner"></a>具名實體辨識 (NER)
具名實體辨識 (NER) 是識別文字中各種不同的實體，並將它們分類至預先定義類別的能力。 下面列出所支援的實體類別。

在 文字分析[2.1 版](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)，實體連結和具名的實體辨識 (NER) 可供數種語言。 請參閱[語言支援](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)文件，如需詳細資訊。 

### <a name="language-support"></a>語言支援

若以多種語言使用連結實體，則需使用每一種語言的對應知識庫。 對於文字分析中的實體連結，這表示 `entities` 端點支援的每一種語言會連結至該語言的對應維基百科語料庫。 由於語料庫的大小會因語言不同而有所差異，因此我們預期實體連結功能的召回也會有所不同。

## <a name="supported-types-for-named-entity-recognition"></a>針對具名實體辨識所支援的類型

| 類型  | SubType | 範例 |
|:-----------   |:------------- |:---------|
| Person        | N/A\*         | "Jeff"、"Bill Gates"     |
| Location      | N/A\*         | "Redmond, Washington"、"Paris"  |
| 組織  | N/A\*         | "Microsoft"   |
| 數量      | 數字        | "6"、"six"     | 
| 數量      | 百分比    | "50%"、"fifty percent"| 
| 數量      | 序號       | "2nd"、"second"     | 
| 數量      | 數字範圍   | "4 to 8"     | 
| 數量      | Age           | "90 day old"、"30 years old"    | 
| 數量      | Currency      | "$10.99"     | 
| 數量      | 維度     | "10 miles"、"40 cm"     | 
| 數量      | 溫度   | "32 degrees"    |
| Datetime      | N/A\*         | "6:30PM February 4, 2012"      | 
| Datetime      | Date          | "May 2nd, 2017"、"05/02/2017"   | 
| Datetime      | Time          | "8am"、"8:00"  | 
| Datetime      | 日期範圍     | "May 2nd to May 5th"    | 
| Datetime      | 時間範圍     | "6pm to 7pm"     | 
| Datetime      | Duration      | "1 minute and 45 seconds"   | 
| Datetime      | 設定           | "every Tuesday"     | 
| Datetime      | TimeZone      |    | 
| URL           | N/A\*         | "https:\//www.bing.com"    |
| Email         | N/A\*         | "support@contoso.com" |

\* 依輸入和擷取的實體而定，某些實體可能會省略 `SubType`。  僅適用於英文、 簡體中文、 法文、 德文和西班牙文語言時，所列的所有支援的實體類型。



## <a name="preparation"></a>準備工作

您必須具有此格式的 JSON 文件：識別碼、文字、語言

如需目前支援的語言，請參閱[這份清單](../text-analytics-supported-languages.md)。

文件大小必須少於 5,120 個字元，而且您最多可以針對每個集合擁有 1,000 個項目 (識別碼)。 集合會在要求本文中提交。 下列範例是您可能會提交至實體連結末端的內容說明。

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>步驟 1：建立要求結構

關於要求定義的詳細資料可以在[如何呼叫文字分析 API](text-analytics-how-to-call-api.md) 中找到。 為了方便起見，我們將重申下列各點：

+ 建立一個 **POST** 要求。 檢閱適用於此要求的 API 文件：[實體連結 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ 設定適用於實體擷取的 HTTP 端點。 它必須包括 `/entities` 資源：`https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1/entities`

+ 設定要求標頭以包含適用於文字分析作業的存取金鑰。 如需詳細資訊，請參閱[如何尋找端點和存取金鑰](text-analytics-how-to-access-key.md)。

+ 在要求本文中，提供您準備用於此分析的 JSON 文件集合

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟[文件](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) \(英文\) 中的 **API 測試主控台**來建立要求結構，並將它 POST 到服務。

## <a name="step-2-post-the-request"></a>步驟 2：張貼要求

分析會在接收要求時執行。 請參閱[資料限制](../overview.md#data-limits)的大小和您可以傳送每分鐘和秒的要求數目相關資訊的概觀一節。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。

## <a name="step-3-view-results"></a>步驟 3：檢視結果

所有的 POST 要求都會傳回 JSON 格式的回應，具有識別碼和偵測到的屬性。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。

實體連結輸出的範例會隨即顯示：

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```


## <a name="summary"></a>總結

在本文中，您已了解在認知服務中使用文字分析的實體連結概念和工作流程。 摘要說明：

+ [實體 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) \(英文\) 僅針對特定語言提供。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ 使用對您訂用帳戶有效的個人化[存取金鑰和端點](text-analytics-how-to-access-key.md)，將要求 POST 到 `/entities` 端點。
+ 由連結實體 (包含每個文件識別碼的信賴分數、位移和網頁連結) 組成的回應輸出可用於任何應用程式

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [文字分析概觀](../overview.md)  
* [常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
* [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712) 
