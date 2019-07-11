---
title: 語音轉換文字 API 參考 (REST)-語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音轉換文字 REST API。 在本文中，您會了解到授權選項、查詢選項，以及如何建構要求與接收回應。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9d967fa4d5ba54e4470dadc5e797067454e1769a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606346"
---
# <a name="speech-to-text-rest-api"></a>語音轉換文字 REST API

做為替代[語音 SDK](speech-sdk.md)，語音服務可讓您將語音轉換文字使用 REST API。 每個可存取的端點皆與區域相關聯。 您的應用程式需要您打算使用之端點的訂用帳戶金鑰。

之前使用語音轉換文字 REST API，了解：
* 使用 REST API 的要求只能包含 10 秒的錄製音訊。
* 語音轉文字 REST API 只會傳回最終結果， 不提供部分的結果。

如果您的應用程式需要傳送更長的音訊，請考慮使用[語音 SDK](speech-sdk.md) 或[批次轉譯](batch-transcription.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>區域與端點

支援使用 REST API 對以下區域進行語音轉文字的文字轉錄。 請確定選取的是符合您訂用帳戶區域的端點。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>查詢參數

REST 要求的查詢字串中可能包括這些參數。

| 參數 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `language` | 識別正在辨識的口說語言。 請參閱[支援的語言](language-support.md#speech-to-text)。 | 必要 |
| `format` | 指定結果格式。 接受的值為 `simple` 和 `detailed`。 簡單的結果包含 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 詳細的回應包含多個具有信賴值的結果和四個不同的表示法。 預設設定為 `simple`。 | 選擇性 |
| `profanity` | 指定如何處理辨識結果中的不雅內容。 接受的值為 `masked` (以星號取代不雅內容)、`removed` (移除結果中所有的不雅內容)，或 `raw` (結果中包含不雅內容)。 預設設定為 `masked`。 | 選擇性 |

## <a name="request-headers"></a>要求標頭

下表列出了語音轉文字要求的必要標頭和選用標頭。

|標頭| 描述 | 必要/選用 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 您的語音服務訂用帳戶金鑰。 | 必須有此標頭或 `Authorization`。 |
| `Authorization` | 前面加入 `Bearer` 這個字的授權權杖。 如需詳細資訊，請參閱[驗證](#authentication)。 | 必須有此標頭或 `Ocp-Apim-Subscription-Key`。 |
| `Content-type` | 描述所提供音訊資料的格式和轉碼器。 接受的值為 `audio/wav; codecs=audio/pcm; samplerate=16000` 和 `audio/ogg; codecs=opus`。 | 必要項 |
| `Transfer-Encoding` | 指定正在傳送的音訊資料區塊，而不是單一檔案。 只有在以區塊處理音訊資料時，才能使用此標頭。 | 選擇性 |
| `Expect` | 如果使用區塊傳輸，請傳送 `Expect: 100-continue`。 語音服務會確認初始要求，並且等候其他資料。| 如果傳送的是音訊資料區塊，則為必要。 |
| `Accept` | 如果提供，則必須是 `application/json`。 語音服務提供 JSON 中的結果。 如果您未指定預設值，則有些 Web 要求架構會提供不相容的預設值，因此一律包含 `Accept` 是很好的做法。 | 此為選用步驟，但建議執行。 |

## <a name="audio-formats"></a>自動格式

音訊是在 HTTP `POST` 要求的主體中傳送。 它必須是此表格中的格式之一：

| 格式 | 轉碼器 | Bitrate | 採樣速率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位元 | 16 kHz，單聲道 |
| OGG | OPUS | 16 位元 | 16 kHz，單聲道 |

>[!NOTE]
>透過 REST API 和語音服務中的 WebSocket 支援上述的格式。 [語音 SDK](speech-sdk.md) 目前僅支援具備 PCM 轉碼器的 WAV 格式。

## <a name="sample-request"></a>範例要求

這是一般的 HTTP 要求。 下列範例包含主機名稱和必要標頭。 請務必注意，此服務也預期會有此範例中未包含的音訊資料。 如先前所述，建議以區塊處理，但非必要。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP 狀態碼

每個回應的 HTTP 狀態碼會指出成功或常見的錯誤。

| HTTP 狀態碼 | 描述 | 可能的原因 |
|------------------|-------------|-----------------|
| 100 | Continue | 已接受初始要求。 繼續傳送其餘的資料。 (搭配區塊傳輸使用。) |
| 200 | [確定] | 要求成功；回應主體是 JSON 物件。 |
| 400 | 不正確的要求 | 未提供語言代碼，或不是支援的語言；無效的音訊檔案。 |
| 401 | 未經授權 | 訂用帳戶金鑰或授權權杖在指定的區域中無效，或是無效的端點。 |
| 403 | 禁止 | 遺漏訂用帳戶金鑰或授權權杖。 |

## <a name="chunked-transfer"></a>區塊傳輸

區塊傳輸 (`Transfer-Encoding: chunked`) 可協助降低辨識延遲，因為它可讓開始處理音訊檔案，它是傳送語音服務。 REST API 不會提供部分或中間的結果。 此選項僅僅為了改善回應能力。

此程式碼範例說明如何以區塊傳送音訊。 只有第一個區塊應該包含音訊檔案的標頭。 `request` 是連線到適當 REST 端點的 HTTPWebRequest 物件。 `audioFile` 是音訊檔案在磁碟上的路徑。

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>回應參數

結果以 JSON 格式提供。 `simple` 格式包含以下的最上層欄位。

| 參數 | 說明  |
|-----------|--------------|
|`RecognitionStatus`|狀態，例如 `Success` 代表辨識成功。 請參閱下一個表格。|
|`DisplayText`|在大寫字、標點符號、反向文字正規化 (將語音文字轉換為較短的形式，例如 200 表示 "two hundred" 或將 "Dr.Smith" 表示 "doctor smith")，以及不雅內容遮罩之後辨識的文字。 只會在成功時呈現。|
|`Offset`|辨識的語音在音訊資料流中開始的時間 (以 100 奈秒為單位)。|
|`Duration`|辨識的語音在音訊資料流中的持續時間 (以 100 奈秒為單位)。|

`RecognitionStatus` 欄位可包含下列的值：

| 狀態 | 說明 |
|--------|-------------|
| `Success` | 辨識成功並顯示 `DisplayText` 欄位。 |
| `NoMatch` | 音訊串流中偵測到語音，但目標語言中沒有符合的字組。 通常表示辨識語言與使用者的口語語言不同。 |
| `InitialSilenceTimeout` | 音訊串流的開頭沒有任何聲音，而且等候語音的服務已逾時。 |
| `BabbleTimeout` | 音訊串流的開頭只有雜音，而且等候語音的服務已逾時。 |
| `Error` | 辨識服務發生內部錯誤，無法繼續。 可能的話，再試一次。 |

> [!NOTE]
> 如果音訊只包含不雅內容，而且 `profanity` 查詢參數設為 `remove`，則服務不會傳回語音結果。

`detailed`格式包含相同的資料`simple`格式，以及使用`NBest`，替代的解譯，相同的辨識結果的清單。 這些結果會從最有可能項目，到最不可能。 第一個項目與主要辨識結果相同。  使用 `detailed` 格式時，系統會提供 `DisplayText` 作為 `NBest` 清單中每個結果的 `Display`。

`NBest` 清單中的每個物件包括：

| 參數 | 說明 |
|-----------|-------------|
| `Confidence` | 項目的信賴分數從 0.0 (不信賴) 到 1.0 (完全信賴) |
| `Lexical` | 已辨識文字的語彙形式：已辨識的實際文字。 |
| `ITN` | 已辨識文字的反向文字正規化 (「標準」) 形式，包含電話號碼、數字、縮寫 ("doctor smith" 縮短為 "dr smith")，以及其他已套件的轉換。 |
| `MaskedITN` | 如果要求，已套用不雅內容遮罩的 ITN 形式。 |
| `Display` | 已辨識文字的顯示形式，已新增標點符號和大寫。 此參數與當格式設定為 `simple` 時，所提供的 `DisplayText` 相同。 |

## <a name="sample-responses"></a>回應範例

以下是 `simple` 辨識的典型回應。

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

以下是 `detailed` 辨識的典型回應。

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
