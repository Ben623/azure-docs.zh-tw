---
title: 範例：在認知搜尋管線中建立自訂技能 - Azure 搜尋服務
description: 示範在對應到 Azure 搜尋服務認知搜尋索引管線的自訂技能中使用文字翻譯 API。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: efa85491f4b183a044ec5d9e5e6e3d11eebedbe3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428443"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>範例：建立使用文字翻譯 API 的自訂技能

在此範例中，了解如何建立 web API 自訂技能。 這項技術會接受以任何語言的文字，並將它轉譯為英文。 此範例使用 [Azure 函式](https://azure.microsoft.com/services/functions/)來包裝[翻譯文字 API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)，以便實作自訂的技能介面。

## <a name="prerequisites"></a>必要條件

+ 了解[自訂技能介面](cognitive-search-custom-skill-interface.md)發行項，如果您不熟悉如何使用自訂的技術應該實作的輸入/輸出介面。

+ [註冊翻譯文字 API](../cognitive-services/translator/translator-text-how-to-signup.md)，並取得 API 金鑰以使用 API。

+ 安裝[Visual Studio 2019](https://www.visualstudio.com/vs/)或更新版本，包括 Azure 開發工作負載。

## <a name="create-an-azure-function"></a>建立 Azure 函式

雖然此範例會使用 Azure Function 裝載 web API，不需要它。  只要您符合[認知技能的介面需求](cognitive-search-custom-skill-interface.md)，採取的方式並不重要。 不過，Azure Functions 能讓您輕鬆建立自訂技能。

### <a name="create-a-function-app"></a>建立函數應用程式

1. 在 Visual Studio 中，從 [檔案] 功能表中選取 [新增]   > [專案]  。

1. 在 [新增專案] 對話方塊中，選取 [已安裝]  ，展開 [Visual C#]   > [雲端]  ，選取 [Azure Functions]  ，輸入專案的 [名稱]，然後選取 [確定]  。 函式應用程式名稱必須是有效的 C# 命名空間，因此不會使用底線、連字號或任何其他非英數字元。

1. 選取  **Azure Functions v2 (.NET Core)** 。 您也可以使用第 1 版來執行，但下方撰寫的程式碼以 v2 範本為基礎。

1. 選取 [HTTP 觸發程序]  類型

1. 對於儲存體帳戶，您可以選取 [無]  ，因為這個函式不需要任何儲存體。

1. 選取 [確定]  以建立函式專案和 HTTP 觸發函式。

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>修改程式碼以呼叫翻譯認知服務

Visual Studio 會建立一個專案，其中的類別包含所選函式類型的重複使用程式碼。 方法上的 *FunctionName* 屬性會設定函式名稱。 *HttpTrigger* 屬性會指定由 HTTP 要求觸發函式。

現在，將檔案 *Function1.cs* 的所有內容取代為下列程式碼：

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

請務必根據註冊翻譯文字 API 時取得的金鑰，在 *TranslateText* 方法中輸入您自己的 *key* 值。

這個範例是一個簡單的擴充程式，一次只能處理一筆記錄。 這在之後設定技能集的批次大小時會變得很重要。

## <a name="test-the-function-from-visual-studio"></a>從 Visual Studio 測試函式

按 **F5** 以執行程式並測試函式行為。 在此情況下，我們將使用下列函式來翻譯成英文的西班牙文的文字。 使用 Postman 或 Fiddler 來發出呼叫，如下所示：

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Request body
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Response
您應該會看到類似於以下範例的回應：

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>將函式發佈至 Azure

當您滿意的函數行為時，您可以發行分支。

1. 在 [方案總管]  中，以滑鼠右鍵按一下專案並選取 [發佈]  。 選擇 [建立新項目]   > [發佈]  。

1. 如果您尚未將 Visual Studio 連線到您的 Azure 帳戶，請選取 [新增帳戶...]  。

1. 遵循螢幕上的提示進行。 系統會要求您指定的 Azure 帳戶、 資源群組、 主控方案，以及您想要使用的儲存體帳戶。 如果您沒有上述項目，則可以建立新的資源群組、新的主控方案和儲存體帳戶。 完成後，請選取 [建立] 

1. 完成部署之後，請注意網站 URL。 這是 Azure 中的函數應用程式的位址。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至資源群組，並尋找您發佈的翻譯函式。 在 [管理]  區段下，應該會看到主機金鑰。 選取 [預設]  主機金鑰的 [複製]  圖示。  

## <a name="test-the-function-in-azure"></a>在 Azure 測試函式

有了預設主機金鑰之後，請如下列所示測試您的函式：

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>要求本文
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

此範例中應會產生類似的結果，以您先前看到的本機環境中執行的函式時。

## <a name="connect-to-your-pipeline"></a>連線到您的管線
有了新的自訂技能之後，就可以將它加入您的技能集。 下列範例示範如何呼叫技能。 因為技能無法處理批次，所以加入批次大小上限只有 ```1``` 的指示，以便一次傳送一個文件。

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>後續步驟
恭喜！ 您已建立您第一個自訂豐富器新增。 現在您可以遵循相同的模式，新增自己的自訂功能。 

+ [將自訂技能新增至認知搜尋管線](cognitive-search-custom-skill-interface.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應擴充的欄位](cognitive-search-output-field-mapping.md)
