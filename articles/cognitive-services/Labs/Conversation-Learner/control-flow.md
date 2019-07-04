---
title: 對話學習模組控制流程 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 深入了解對話學習模組控制流程。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 22a2a3472a54188f9298c580a95d53ac681822aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66385421"
---
## <a name="control-flow"></a>控制流程

本文件說明對話學習模組 (CL) 的控制流程，如下圖所示。

![](media/controlflow.PNG)

1. 使用者在聊天機器人中輸入詞彙或片語，例如，「西雅圖天氣如何？」
1. CL 會將使用者輸入傳至擷取實體的機器學習模型
   - 此模型是由對話學習模組建立，並且由 www.luis.ai 代管
1. 任何擷取的實體及使用者的輸入文字，都會傳輸至聊天機器人程式碼中的實體偵測回呼方法。
    - 此程式碼可設定/清除/操作實體值
1. CL 類神經網路接著會使用實體擷取的輸出和使用者輸入，針對聊天機器人中定義的所有動作進行評分
   - 在此範例中，最可能的動作是提供氣象預報：

     ![](media/controlflow_forecast.PNG)

1. 在此情況下，選定的動作需要 API 呼叫擷取氣象預報。 
1. 然後會叫用已使用 CL.AddCallback 方法註冊的這個 API。  此 API 的結果接著會以訊息型式回傳給使用者，例如，「晴天，高溫 67 度」。
1. 然後會呼叫類神經網路，依照上一個步驟判斷下一個動作。
1. 類神經網路接著會預測後續的可能動作，而且會向使用者顯示選取的動作，像是這邊的「還有其他事嗎？」

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何指導與對話學習模組](./how-to-teach-cl.md)
