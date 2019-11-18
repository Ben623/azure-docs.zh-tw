---
title: 語音轉換文字-語音服務
titleSuffix: Azure Cognitive Services
description: 語音轉換文字功能可將音訊串流的即時轉譯成文字，讓您的應用程式、工具或裝置可以取用、顯示和採取動作作為命令輸入。 此服務可與文字轉換語音（語音合成），以及語音翻譯功能緊密搭配運作。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49bfa4a0dbf0adc498d545a2908c20f0ffa35b4b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075724"
---
# <a name="what-is-speech-to-text"></a>什麼是語音轉換文字？

來自 Azure 語音服務的語音轉換文字（也稱為語音轉換文字）可將音訊串流即時轉譯成文字，讓您的應用程式、工具或裝置可以取用、顯示和採取動作作為命令輸入。 這項服務是由 Microsoft 用於 Cortana 和 Office 產品的相同辨識技術所支援，而且可與翻譯和文字轉換語音緊密搭配運作。 如需可用語音轉換文字語言的完整清單，請參閱[支援的語言](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)。

根據預設，語音轉換文字服務會使用通用語言模型。 此模型已使用 Microsoft 所擁有的資料進行定型，並已部署在雲端中。 這對於對話式和聽寫案例而言是最理想的。 如果您在獨特的環境中使用語音轉文字進行辨識及轉譯，您可以建立並定型自訂原音、語言和發音模型，以處理環境噪音或業界專有詞彙。

您可以使用語音 SDK 和 REST Api，輕鬆地從麥克風捕獲音訊、從串流讀取，或從儲存體存取音訊檔案。 語音 SDK 支援以 WAV/PCM 16 位元、16 kHz/8 kHz 單一通道進行語音辨識。 您可使用[語音轉換文字 REST 端點](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)或[批次抄寫服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)來支援其他音訊格式。

## <a name="core-features"></a>核心功能

以下是透過語音 SDK 和 REST Api 提供的功能：

| 使用案例 | SDK | REST |
|--------- | --- | ---- |
| 轉譯 short 語句（< 15 秒）。 僅支援最終轉譯結果。 | yes | yes |
| 長語句和串流音訊的連續轉譯（> 15 秒）。 支援過渡和最終轉譯結果。 | yes | 否 |
| 使用[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)從辨識結果衍生意圖。 | yes | 否\* |
| 以非同步方式對音訊檔案進行批次轉譯。 | 否  | 是\*\* |
| 建立和管理語音模型。 | 否 | 是\*\* |
| 建立和管理自訂模型部署。 | 否  | 是\*\* |
| 建立精確度測試，以測量基準模型與自訂模型的精確度。 | 否  | 是\*\* |
| 管理訂閱。 | 否  | 是\*\* |

\*_LUIS 意圖和實體可以使用個別的 LUIS 訂用帳戶來衍生。使用此訂用帳戶，SDK 可以為您呼叫 LUIS，並提供實體和意圖結果。有了 REST API，您就可以自行呼叫 LUIS，以使用您的 LUIS 訂用帳戶來衍生意圖和實體。_

\*\*_這些服務可以使用 cris.ai 端點來取得。請參閱[Swagger 參考](https://westus.cris.ai/swagger/ui/index)。_

## <a name="get-started-with-speech-to-text"></a>開始使用語音轉換文字

我們以最受歡迎的程式設計語言提供快速入門，目的是讓您能在 10 分鐘內執行程式碼。 下[表](https://aka.ms/csspeech#5-minute-quickstarts)包含依平臺和語言組織的語音 SDK 快速入門完整清單。 您也可以在[這裡](https://aka.ms/csspeech#reference)找到 API 參考。

如果您想要使用語音轉換文字 REST 服務，請參閱[Rest api](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)。

## <a name="tutorials-and-sample-code"></a>教學課程和範例程式碼

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

- [教學課程：使用語音 SDK 和 LUIS 從語音辨識意圖。C#](how-to-recognize-intents-from-speech-csharp.md)

語音 SDK 的範例程式碼可在 GitHub 上取得。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。

- [語音轉換文字範例（SDK）](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>自訂

除了語音服務所使用的標準基準模型之外，您還可以使用可用資料自訂模型以滿足您的需求，以克服語音辨識的阻礙，例如說話風格、詞彙和背景雜音，請參閱[自訂語音](how-to-custom-speech.md)

> [!NOTE]
> 自訂選項會因語言/地區設定而有所不同（請參閱[支援的語言](supported-languages.md)）。

## <a name="migration-guides"></a>移轉指南

> [!WARNING]
> Bing 語音已于2019年10月15日解除委任。

如果您的應用程式、工具或產品使用 Bing 語音 Api 或自訂語音，我們建立了指南，協助您遷移至語音服務。

- [從 Bing 語音遷移至語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [從自訂語音遷移至語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>參考文件

- [語音 SDK](https://aka.ms/csspeech)
- [語音裝置 SDK](speech-devices-sdk.md)
- [REST API：語音轉換文字](rest-speech-to-text.md)
- [REST API：文字轉換語音](rest-text-to-speech.md)
- [REST API：批次轉譯和自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

- [免費取得語音服務的訂用帳戶金鑰](get-started.md)
- [取得語音 SDK](speech-sdk.md)
