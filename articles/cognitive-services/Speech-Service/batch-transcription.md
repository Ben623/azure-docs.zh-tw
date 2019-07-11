---
title: 如何使用批次轉譯 - 語音服務
titlesuffix: Azure Cognitive Services
description: 如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: b71400c3ae3c1cc6737d9194b4d94bf0b9c7efa9
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606734"
---
# <a name="why-use-batch-transcription"></a>為何使用 Batch 轉譯？

如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。

## <a name="prerequisites"></a>先決條件

### <a name="subscription-key"></a>訂用帳戶金鑰

如同語音服務的所有功能，您可以依照我們的[快速入門指南](get-started.md)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。 如果您打算從我們的基準模型取得轉譯，您只要建立金鑰即可。

>[!NOTE]
> 語音服務的標準訂用帳戶 (S0) 才能使用批次轉譯。 免費訂用帳戶金鑰 (F0) 將無法運作。 如需詳細資訊，請參閱[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自訂模型

如果您打算自訂原音或語言模型，請依照[自訂原音模型](how-to-customize-acoustic-models.md)和[自訂語言模型](how-to-customize-language-model.md)中的步驟進行。 若要使用批次轉譯中建立的模型，您需要其模型識別碼。 此識別碼不是您在 [端點詳細資料] 檢視上所找到的端點識別碼，這是您選取該模型的詳細資料時可擷取的模型識別碼。

## <a name="the-batch-transcription-api"></a>Batch 轉譯 API

Batch 轉譯 API 提供非同步語音轉換文字轉譯及其他功能。 此 REST API 所公開的方法可以：

1. 建立批次的處理要求
1. 查詢狀態
1. 下載轉譯

> [!NOTE]
> Batch 轉譯 API 很適合話務中心，它們通常會累積數千小時的音訊。 這能輕鬆轉譯大量的音訊錄製。

### <a name="supported-formats"></a>支援的格式

Batch 轉譯 API 支援下列格式：

| 格式 | 轉碼器 | Bitrate | 採樣速率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |
| MP3 | PCM | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |
| OGG | OPUS | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |

針對立體聲音訊資料流，Batch 轉譯 API 會在轉譯期間分離左右聲道。 這會建立兩個 JSON 結果檔案，每個聲道各建立一個。 每個語句的時間戳記可讓開發人員建立排序的最終文字記錄。 此範例要求包含不雅內容篩選、標點符號和字組層級時間戳記的屬性。

### <a name="configuration"></a>組態

組態參數以 JSON 格式提供：

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> Batch 轉譯 API 使用 REST 服務來要求轉譯、其狀態及相關結果。 您可以使用任何語言的 API。 下一節描述 API 的使用方式。

### <a name="configuration-properties"></a>組態屬性

您可以使用這些選擇性屬性來設定文字記錄：

| 參數 | 描述 |
|-----------|-------------|
| `ProfanityFilterMode` | 指定如何處理辨識結果中的不雅內容。 接受的值為 `none` (會停用不雅內容過濾)、`masked` (為以星號取代不雅內容)、`removed` (會移除結果中的所有不雅內容) 或 `tags` (會新增「不雅內容」標記)。 預設設定為 `masked`。 |
| `PunctuationMode` | 指定如何處理辨識結果中的標點符號。 接受的值為`none` (會停用標點符號)、`dictated` (暗示明確的標點符號)、`automatic` (會讓解碼器處理標點符號) 或 `dictatedandautomatic` (暗示口述的標點符號或自動)。 |
 | `AddWordLevelTimestamps` | 指定是否將字組層級時間戳記新增至輸出。 接受的值為`true` 會啟用字組層級時間戳記，而 `false` (預設值) 會停用。 |
 | `AddSentiment` | 指定 人氣應新增至 utterance。 接受的值為`true`可讓每個 [utterance] 的人氣和`false`加以停用的 （預設值）。 |
 | `AddDiarization` | 指定該 diarization alalysis 應該必須是單聲道包含兩個語音輸入上執行。 接受的值為`true`可讓 diarization 和`false`加以停用的 （預設值）。 它也需要`AddWordLevelTimestamps`設為 true。|

### <a name="storage"></a>儲存體

Batch 轉譯支援[Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)讀取音訊 」 和 「 寫入至儲存體的轉譯。

## <a name="webhooks"></a>webhooks

輪詢程式可能不會轉譯狀態的最有效，或提供最佳使用者體驗。 若要輪詢狀態，您可以註冊長時間執行轉譯工作完成時，會通知用戶端的回呼。

如需詳細資訊，請參閱 < [Webhook](webhooks.md)。

## <a name="speaker-separation-diarization"></a>說話者區隔 (Diarization)

Diarization 是音訊的區隔喇叭的程序。 我們的批次管線支援 Diarization，而且能夠辨識兩個喇叭單聲道記錄上。

若要要求音訊轉譯要求針對 diarization 進行處理，您只需要在 HTTP 要求中新增相關的參數，如下所示。

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

因為上述要求中的參數表示 Word 層級的時間戳記也必須以 '開啟'。

對應的音訊會包含以編號識別的說話者 (目前，我們支援只有兩個語音，因此喇叭會被識別為 '演講者備忘 1' 和 ' 演講者備忘 2') 後面接著轉譯輸出。

也請注意 Diarization 立體聲音錄製中沒有。 此外，所有的 JSON 輸出就會包含演講者備忘標記。 如果未使用 diarization，它會顯示 ' 演講者：Null' JSON 輸出中。

> [!NOTE]
> 在所有區域，以及針對所有地區設定，diarization 已上市 ！

## <a name="sentiment"></a>情感

情感是 Batch 轉譯 API 中的新功能，在呼叫 center 網域中的重要功能。 客戶可以使用`AddSentiment`其要求的參數

1.  客戶滿意度上取得深入解析
2.  取得深入解析 （小組採取呼叫） 的代理程式的效能
3.  找出的時間時呼叫一回合中以負數方向中的精確點
4.  找出開啟負正呼叫時，也有何
5.  識別客戶的喜歡和其相關產品或服務不喜歡

情感評分每個音訊區段其中的音訊區段定義為 [utterance] （位移） 的開始之間的位元組資料流的結尾偵測無回應的時間間隔。 該區段中的整個文字用來計算情感。 我們並 「 不計算任何彙總情緒值的整個呼叫或整個語音的每一個色頻。 這些彙總可供網域擁有者，來進一步套用。

人氣已套用的語彙格式。

JSON 輸出範例如下所示：

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
此功能會使用情感的模型，它目前仍處於 beta 版。

## <a name="sample-code"></a>範例程式碼

完整的範例都可以從[GitHub 範例存放庫](https://aka.ms/csspeech/samples)內`samples/batch`子目錄。

您自訂的範例程式碼要有訂用帳戶資訊、服務區域、指向音訊檔的 SAS URI 以轉譯，以及模型識別碼，以防您想要使用自訂原音或語言模型。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

範例程式碼將設定用戶端並提交轉譯要求。 接著會輪詢狀態資訊並列印轉譯進度的詳細資料。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

如需上述呼叫的完整詳細資訊，請參閱我們的 [Swagger 文件](https://westus.cris.ai/swagger/ui/index)。 如需此處所顯示的完整範例，請前往 `samples/batch` 子目錄中的 [GitHub](https://aka.ms/csspeech/samples)。

請留意張貼音訊和接收轉譯狀態的非同步設定。 您建立的用戶端是 .NET HTTP 用戶端。 `PostTranscriptions` 方法可傳送音訊檔案詳細資料，而 `GetTranscriptions` 方法可接收結果。 `PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

目前的範例程式碼沒有指定自訂模型。 該服務會使用基準模型來轉譯一或多個檔案。 若要指定模型，您可以傳遞相同的方法，如同原音和語言模型的模型識別碼。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的識別碼。 如果您只有指定語言模型識別碼 (且沒有任何的原音模型識別碼)，會自動選取相符的原音模型。 如果您只有指定原音模型識別碼，則會自動選取相符的語言模型。

## <a name="download-the-sample"></a>下載範例

您可以在 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的 `samples/batch` 目錄找到範例。

> [!NOTE]
> 批次轉譯作業會盡可能排程，作業何時會變更為執行狀態則無法預估。 只要處於執行中狀態，實際轉譯的即時處理速度會比音訊快。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
