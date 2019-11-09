---
title: 使用影片索引子自動識別說話語言-Azure
titleSuffix: Azure Media Services
description: 本文說明如何使用影片索引子語言識別模型，在影片中自動識別說話的語言。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: ce3e488a6387f9a823d7c1b514b52af24944776b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838999"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>使用語言識別模型自動識別說話語言

影片索引子支援自動語言識別（蓋子），這是自動從音訊識別說話語言內容，以及傳送要以主要識別語言轉譯之媒體檔案的程式。 目前的蓋子支援英文、西班牙文、法文、德文、義大利文、中文（簡體）、日文、俄文和葡萄牙文（巴西）。 

## <a name="choosing-auto-language-identification-on-indexing"></a>選擇編制索引時自動語言識別

使用 API 編制影片的索引或[重新編制](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)索引時，請選擇 `sourceLanguage` 參數中的 [`auto detect`] 選項。

使用入口網站時，請移至[影片索引子](https://www.videoindexer.ai/)首頁上的**帳戶**影片，並將滑鼠停留在您想要重新編制索引的影片名稱上方。 按一下右下角的 [重新編制索引] 按鈕。 在 [**重新編制索引] 影片**對話方塊中，從 [**影片來來源語言**] 下拉式方塊中選擇 [*自動*偵測]。

![自動偵測](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型輸出

影片索引子會根據最可能的語言來可將影片（如果該語言的信心 `> 0.6`）。 如果無法自信地識別語言，則會假設說語言是英文。 

模型主要語言可在深入解析 JSON 中取得，做為 `sourceLanguage` 屬性（在 [根/影片/深入解析] 底下）。 在 [`sourceLanguageConfidence`] 屬性下也會提供對應的信賴分數。

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>指導方針和限制

* 支援的語言包括英文、西班牙文、法文、德文、義大利文、中文（簡體）、日文、俄文和巴西葡萄牙文。
* 如果音訊包含上述支援清單以外的語言，則會產生非預期的結果。
* 如果影片索引子無法識別具有足夠信賴度（`>0.6`）的語言，則回復語言為英文。
* 目前不支援混合語言為音訊的檔案。 如果音訊包含混合語言，則會產生非預期的結果。 
* 低品質的音訊可能會影響模型結果。
* 此模型在音訊中至少需要一分鐘的語音。
* 此模型的設計目的是要辨識自發對話語音（非語音命令、唱歌等等）。

## <a name="next-steps"></a>後續步驟

* [概觀](video-indexer-overview.md)
* [自動識別並轉譯多語言內容](multi-language-identification-transcription.md)
