---
title: 改善知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 主動式學習可用來根據使用者提交的內容提出建議的問答組替代問題，以提高知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。 您的知識庫不會自動變更。 您必須接受建議，變更才會生效。 這些建議會新增問題，但不會變更或移除現有的問題。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: diberry
ms.openlocfilehash: b5528d8cd23893248170bdb15588925f3c92c02b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934707"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>使用主動式學習來改善您的知識庫

主動式學習可用來根據使用者提交的內容提出建議的問答組替代問題，以提高知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。 

您的知識庫不會自動變更。 為了讓變更生效，您必須接受建議。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="what-is-active-learning"></a>什麼是主動式學習？

QnA Maker 可透過隱含和明確的意見反應學習新演變出來的問題。
 
* [隱含的意見](#how-qna-makers-implicit-feedback-works)反應– ranker 瞭解使用者問題有多個答案的分數非常接近，並將此視為意見反應。 您不需要執行任何動作，就能進行這種情況。
* [明確的意見](#how-you-give-explicit-feedback-with-the-train-api)反應：當分數中有小變化的多個答案從知識庫傳回時，用戶端應用程式會詢問使用者哪個問題是正確的問題。 使用者的明確意見反應會透過[訓練 API](#train-api)傳送給 QnA Maker。 

這兩種方法都會提供具有叢集之類似查詢的 ranker。

## <a name="how-active-learning-works"></a>主動式學習的運作方式

主動式學習會根據 QnA Maker 所傳回的前幾個答案的分數來觸發。 如果分數差異落在較小的範圍內，則查詢會被視為每個可能的 QnA 配對的可能建議（做為替代問題）。 一旦您接受特定 QnA 配對的建議問題，則會拒絕其他配對。 在接受建議之後，您必須記得儲存並定型。

當端點取得合理數量和足夠類型的使用查詢時，主動式學習可提供最佳的可能建議。 當有5個或更多類似的查詢叢集化時，每隔30分鐘 QnA Maker，就會針對要接受或拒絕的知識庫設計工具建議以使用者為基礎的問題。 所有建議會依相似度叢集化在一起，並根據使用者發出特定查詢的頻率顯示替代問題的最高排名建議。

在 QnA Maker 入口網站中建議問題後，您必須複習並接受或拒絕這些建議。 沒有可管理建議的 API。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 的隱含意見反應如何運作

QnA Maker 的隱含意見反應會使用演算法來判斷分數鄰近性，然後進行主動式學習建議。 判定相近程度的演算法並非簡單的計算。 下列範例中的範圍不是固定的，但應該做為參考，以瞭解演算法的影響。

當問題的分數具有高信賴度時 (例如 80%)，可考慮進行主動式學習的分數範圍較廣，大約在 10% 以內。 信賴分數下降時 (例如 40%)，分數的範圍也會隨之降低，大約在 4% 以內。 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何使用訓練 API 提供明確的意見反應

請務必 QnA Maker 取得有關答案最佳解答的明確意見反應。 判斷最佳答案的方式由您決定，而且可以包括：

* 使用者意見反應，選取其中一個答案。
* 商務邏輯，例如判斷可接受的分數範圍。  
* 使用者意見反應和商務邏輯的組合。

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>升級您的執行階段版本以使用主動式學習

執行階段 4.4.0 版和更新版本可支援主動式學習。 如果您的知識庫是在舊版中建立的，請[升級執行階段](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以使用這項功能。 

## <a name="turn-on-active-learning-to-see-suggestions"></a>開啟主動式學習以查看建議

依預設會關閉主動式學習。 開啟它以查看建議的問題。 開啟主動式學習之後，您必須將用戶端應用程式的資訊傳送至 QnA Maker。 如需詳細資訊，請參閱[使用 GenerateAnswer 和從 Bot 訓練 api 的架構流程](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 選取 [**發行**] 以發佈知識庫。 只會從 GenerateAnswer API 預測端點收集主動式學習查詢。 QnA Maker 入口網站中 [測試] 窗格的查詢不會影響主動式學習。

1. 若要在 QnA Maker 入口網站中開啟作用中的學習，請移至右上角，並選取您的**名稱**，然後移至 [[**服務設定**](https://www.qnamaker.ai/UserSettings)]。  

    ![從 [服務設定] 頁面開啟主動式學習的建議問題替代方案。 在右上方的功能表中選取您的使用者名稱，然後選取 [服務設定]。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 尋找 QnA Maker 服務，然後切換**主動式學習**。 

    [![在 [服務設定] 頁面上，開啟 [主動式學習] 功能。如果您無法切換此功能，您可能需要升級您的服務。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上述影像上的確切版本只會顯示為範例。 您的版本可能不同。 

    啟用**主動式學習**之後，知識庫會根據使用者提交的問題，定期提供新問題的建議。 您可以再次切換設定以停用**主動式學習**。

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>接受知識庫中的主動式學習建議

1. 若要查看建議的問題，請在 [**編輯**知識庫] 頁面上，選取 [**視圖選項**]，然後選取 [**顯示主動式學習建議**]。 

    [![在入口網站的 [編輯] 區段上，選取 [顯示建議]，以查看主動式學習的新問題替代方案。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 藉由選取 [**依建議篩選**]，篩選具有問題和答案配對的知識庫，僅顯示建議。

    [![使用 [依建議篩選] 切換，即可只查看主動式學習的建議問題替代方案。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 每個 QnA 組都會以核取記號來建議新的問題`✔`替代專案，以接受問題`x`或拒絕建議。 選取核取記號可新增問題。 

    [![選取或拒絕主動式學習的建議問題，方法是選取綠色核取記號或紅色刪除標記。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以在內容相關工具列中選取 [全部**新增**] 或 [**全部拒絕**]，來新增或刪除_所有建議_。

1. 選取 [儲存並訓練]，以儲存對知識庫所做的變更。

1. 選取 [**發佈**]，以允許可從[GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)取得變更。

    當有5個或更多類似的查詢叢集化時，每隔30分鐘 QnA Maker 建議您接受或拒絕的替代問題。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>使用 GenerateAnswer 和從 bot 訓練 Api 的架構流程

Bot 或其他用戶端應用程式應該使用下列架構流程來使用主動式學習：

* Bot 會透過 GenerateAnswer API[從知識庫取得答案](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)，並使用`top`屬性來取得一些答案。
* Bot 會決定明確的意見反應：
    * 使用您自己的[自訂商務邏輯](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)，篩選出低分數。
    * 在 bot 或用戶端應用程式中，顯示使用者可能的解答清單，並取得使用者選取的答案。
* Bot 會使用[訓練 API](#train-api)[將選取的回應傳送回 QnA Maker](#bot-framework-sample-code) 。


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>使用 GenerateAnswer 要求中的 top 屬性來取得數個相符的答案

提交問題給 QnA Maker 以取得解答時，JSON 主體`top`的屬性會設定要傳回的答案數目。 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>使用分數屬性和商務邏輯來取得顯示使用者的答案清單

當用戶端應用程式（例如聊天機器人）收到回應時，就會傳回前3個問題。 `score`使用屬性來分析分數之間的近距離。 這個鄰近範圍是由您自己的商務邏輯所決定。 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>用戶端應用程式在問題具有類似分數時的後續動作

您的用戶端應用程式會顯示問題，並提供選項讓使用者選取最能代表其意圖_的單一問題_。 

當使用者選取其中一個現有的問題時，用戶端應用程式會使用 QnA Maker 的訓練 API，將使用者的選擇傳送至意見反應。 此意見反應會完成主動式學習回饋迴圈。 

## <a name="train-api"></a>將 API 定型

使用訓練 API POST 要求，將主動式學習意見反應傳送至 QnA Maker。 API 簽章為：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 要求屬性|Name|Type|用途|
|--|--|--|--|
|URL 路由參數|知識庫識別碼|string|測試您知識庫的 GUID。|
|裝載子域|QnAMaker 資源名稱|string|您的 Azure 訂用帳戶中 QnA Maker 的主機名稱。 這會在您發佈知識庫之後的 [設定] 頁面上提供。 |
|標頭|Content-Type|string|傳送至 API 的本文媒體類型。 預設值為：`application/json`|
|標頭|Authorization|string|您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|張貼本文|JSON 物件|JSON|訓練意見反應|

JSON 主體有數個設定：

|JSON 主體屬性|Type|用途|
|--|--|--|--|
|`feedbackRecords`|array|意見反應清單。|
|`userId`|string|接受建議問題之人員的使用者識別碼。 使用者識別碼格式是由您負責。 例如，電子郵件地址可以是您架構中的有效使用者識別碼。 選擇性。|
|`userQuestion`|string|使用者查詢的確切文字。 必要。|
|`qnaID`|number|問題的識別碼，可在[GenerateAnswer 回應](metadata-generateanswer-usage.md#generateanswer-response-properties)中找到。 |

範例 JSON 主體如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

成功的回應會傳回狀態204，而且不會傳回 JSON 回應主體。 

### <a name="batch-many-feedback-records-into-a-single-call"></a>將許多意見反應記錄批次成單一呼叫

在用戶端應用程式（例如 bot）中，您可以儲存資料，然後在`feedbackRecords`陣列中的單一 JSON 主體中傳送許多記錄。 

範例 JSON 主體如下所示：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot framework 範例程式碼

如果使用者的查詢應該用於主動式學習，您的 bot framework 程式碼必須呼叫訓練 API。 有兩段程式碼可以撰寫：

* 判斷查詢是否應該用於主動式學習
* 將查詢傳送回 QnA Maker 的訓練 API 以進行主動式學習

在[Azure Bot 範例](https://aka.ms/activelearningsamplebot)中，這兩個活動都已經過編寫。 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>使用C# Bot Framework 4.X 訓練 API 的範例程式碼

下列程式碼說明如何使用定型 API 將資訊傳送回 QnA Maker。 這個[完整的程式碼範例](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore)可從 GitHub 取得。

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>使用 Bot Framework 4.x 訓練 API 的範例 node.js 程式碼 

下列程式碼說明如何使用定型 API 將資訊傳送回 QnA Maker。 這個[完整的程式碼範例](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs)可從 GitHub 取得。

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);
            
            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>主動式學習會儲存在匯出的知識庫中

當您的應用程式啟用主動式學習，並匯出應用程式時， `SuggestedQuestions` tsv 檔案中的資料行會保留使用中的學習資料。 

資料行是隱含、 `autosuggested`和明確`usersuggested`的意見反應之資訊的 JSON 物件。 `SuggestedQuestions` 針對單一使用者提交的問題`help` ，此 JSON 物件的範例為：

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

當您重新匯入此應用程式時，主動式學習會繼續收集資訊，並建議您的知識庫建議。 

## <a name="best-practices"></a>最佳做法

如需使用主動式學習時的最佳做法，請參閱[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="next-steps"></a>後續步驟
 
> [!div class="nextstepaction"]
> [搭配使用中繼資料與 GenerateAnswer API](metadata-generateanswer-usage.md)
