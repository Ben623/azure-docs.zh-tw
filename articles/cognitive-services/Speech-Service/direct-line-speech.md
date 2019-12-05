---
title: Direct Line 語音-語音服務
titleSuffix: Azure Cognitive Services
description: 概述使用語音軟體發展工具組（SDK） Direct Line 語音的語音助理功能、功能和限制。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: cec3131d791d591375fd87d1c080294c9034a815
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806199"
---
# <a name="about-direct-line-speech"></a>關於 Direct Line 語音

[語音助理](voice-assistants.md)會聆聽使用者並採取動作來回應，通常會說回來。 他們使用[語音轉換文字](speech-to-text.md)來轉譯使用者的語音，然後對文字的自然語言理解採取行動。 此動作通常包含以[文字轉換語音](text-to-speech.md)所產生的小幫手的語音輸出。 裝置會使用語音 SDK 的 `DialogServiceConnector` 物件連接到助理。

**Direct Line Speech**是一套強大的端對端解決方案，可讓您建立彈性、可擴充的語音助理，並由 bot Framework 和其 Direct Line 語音通道提供支援，其已針對語音輸入、語音輸出與 bot 的互動而優化。

Direct Line Speech 為語音助理提供最高層級的自訂和複雜的功能。 它非常適合用來完成工作的交談案例，也就是使用工作完成或命令與控制的方式。 這種高度的彈性會產生較大的複雜性，而使用自然語言輸入的範圍為妥善定義工作的案例可能會想要考慮[自訂命令（預覽）](custom-commands.md) ，以提供簡化的解決方案體驗。

## <a name="getting-started-with-direct-line-speech"></a>Direct Line 語音入門

使用 Direct Line 語音建立語音助理的第一個步驟，是[取得語音訂](get-started.md)用帳戶金鑰、建立與該訂用帳戶相關聯的新 bot，然後將 bot 連線至 Direct Line 語音通道。

   ![Direct line speech 協調流程服務流程的概念圖](media/voice-assistants/overview-directlinespeech.png "語音通道流程")

如需使用 Direct Line 語音建立簡單語音助理的完整逐步指南，請參閱[使用語音 SDK 和 Direct Line 語音通道為您的 bot 啟用語音的教學](tutorial-voice-enable-your-bot-speech-sdk.md)課程。

我們也提供的快速入門，是為了讓您在10分鐘內執行程式碼而設計的。 下表包含依語言組織的語音助理快速入門清單。

| 快速入門 | 平台 | API 參考 |
|------------|----------|---------------|
| C#，UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows、macOS、Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上取得用來建立語音助理的範例程式碼。 這些範例涵蓋了用來以數種熱門的程式設計語言連接到您的小幫手的用戶端應用程式。

* [語音助理範例 (SDK)](https://aka.ms/csspeech/samples) \(英文\)
* [教學課程：使用語音 SDK 語音啟用您的助理C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>自訂

使用語音服務所建立的語音助理可以使用適用于[語音轉換文字](speech-to-text.md)、[文字轉換語音](text-to-speech.md)及[自訂關鍵字選取](speech-devices-sdk-create-kws.md)範圍的完整自訂選項。

> [!NOTE]
> 自訂選項會因語言/地區設定而有所不同（請參閱[支援的語言](supported-languages.md)）。

語音助理的 Direct Line 語音和其相關功能，是[虛擬助理解決方案和企業範本](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)的理想補充。 雖然 Direct Line Speech 可以與任何相容的 bot 搭配運作，但這些資源提供高品質對話體驗可重複使用的基準，以及快速入門的常見支援技能和模型。

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
* [建立及部署基本 Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [取得虛擬助理解決方案和企業範本](https://github.com/Microsoft/AI)
