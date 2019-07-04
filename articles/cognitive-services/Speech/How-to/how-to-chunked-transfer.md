---
title: 如何使用區塊傳輸來傳送音訊資料流 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 如何使用區塊傳輸將音訊資料流傳送給 Bing 語音服務
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d9796cf60e2695c21d781131c935d24891401efa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60514996"
---
# <a name="chunked-transfer-encoding"></a>區塊傳輸編碼

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

為了將語音轉譯成文字，Microsoft 語音辨識 API 可讓您將音訊當作一整個區塊來傳送，也可以切割成小區塊來傳送。 為了讓音訊串流處理能夠有效率並縮短轉譯延遲，建議您使用[區塊傳輸編碼](https://en.wikipedia.org/wiki/Chunked_transfer_encoding)將音訊串流至服務。 其他實作可能會讓使用者感覺到較長的延遲。 如需詳細資訊，請參閱[音訊資料流](../concepts.md#audio-streams)頁面。

> [!NOTE]
> 在任一要求中，您都不能上傳超過 10 秒的音訊，總要求持續時間則不可超過 14 秒。
> [!NOTE]
> 只有當您使用 [REST API](../GetStarted/GetStartedREST.md) 來呼叫語音服務時，才需要指定區塊傳輸編碼。 應用程式如果使用[用戶端程式庫](../GetStarted/GetStartedClientLibraries.md)，則不需要設定區塊傳輸編碼。

以下程式碼示範如何設定區塊傳輸編碼，以及如何傳送已切割成 1024 位元組區塊的音訊檔。

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

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
