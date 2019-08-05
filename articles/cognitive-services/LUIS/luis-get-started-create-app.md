---
title: 快速入門：建立應用程式 - LUIS
titleSuffix: Azure Cognitive Services
description: 建立 LUIS 應用程式，該應用程式會使用預先建置網域 `HomeAutomation` 來開啟或關閉燈光和應用程式。 這個預先建置網域可為您提供意圖、實體和範例語句。 完成之後，您會擁有一個在雲端中執行的 LUIS 端點。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e53f8d6e08b345d417ce54deacd658275cb1cd00
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563925"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>快速入門：使用預先建置的家庭自動化應用程式

在本快速入門中，建立 LUIS 應用程式，該應用程式會使用預先建置網域 `HomeAutomation` 來開啟或關閉燈光和應用程式。 這個預先建置網域可為您提供意圖、實體和範例語句。 完成之後，您會擁有一個在雲端中執行的 LUIS 端點。

## <a name="prerequisites"></a>必要條件

在本文中，您需要免費 LUIS 帳戶，在位於 [https://www.luis.ai](https://www.luis.ai) (英文) 的 LUIS 入口網站上建立。 

## <a name="create-a-new-app"></a>建立新的應用程式
您可以在 [我的應用程式]  上建立和管理應用程式。 

1. 登入 LUIS 入口網站。

2. 選取 [建立新的應用程式]  。

    [![應用程式清單的螢幕擷取畫面](media/luis-quickstart-new-app/app-list.png "應用程式清單的螢幕擷取畫面")](media/luis-quickstart-new-app/app-list.png)

3. 在對話方塊中，將您的應用程式命名為 "Home Automation"。

    [![建立新應用程式快顯對話方塊的螢幕擷取畫面](media/luis-quickstart-new-app/create-new-app-dialog.png "建立新應用程式快顯對話方塊的螢幕擷取畫面")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. 選擇您的應用程式文化特性。 請針對這個「家庭自動化」應用程式，選擇 [英文]。 然後選取 [完成]  。 LUIS 會建立「家庭自動化」應用程式。 

    >[!NOTE]
    >建立應用程式之後便無法變更文化特性 (Culture)。 

## <a name="add-prebuilt-domain"></a>新增預建網域

在左側導覽窗格中選取 [預先建置網域]  。 然後搜尋 "Home"。 選取 [新增網域]  。

[![預先建置網域功能表上詳列的家庭自動化網域螢幕擷取畫面](media/luis-quickstart-new-app/home-automation.png "預先建置網域功能表上詳列的家庭自動化網域螢幕擷取畫面")](media/luis-quickstart-new-app/home-automation.png)

當成功新增網域時，預先建置網域方塊會顯示 [移除網域]  按鈕。

[![含有 移除 按鈕的家庭自動化網域螢幕擷取畫面](media/luis-quickstart-new-app/remove-domain.png "含有 移除 按鈕的家庭自動化網域螢幕擷取畫面")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>意圖和實體

在左側瀏覽窗格中選取 [意圖]  ，以檢閱 HomeAutomation 網域意圖。 每個意圖都有範例語句。

![HomeAutomation 意圖清單的螢幕擷取畫面](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation 意圖清單的螢幕擷取畫面")]

> [!NOTE]
> 「無」  是所有 LUIS 應用程式都會提供的意圖。 您可以使用它來處理未對應至應用程式所提供功能的語句。 

選取 **HomeAutomation.TurnOff** 意圖。 您可以看到意圖包含以實體標示的語句清單。

[![HomeAutomation.TurnOff 意圖的螢幕擷取畫面](media/luis-quickstart-new-app/home-automation-turnon.png "HomeAutomation.TurnOff 意圖的螢幕擷取畫面")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>進行 LUIS 應用程式定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>測試應用程式
一旦您定型您的應用程式，就可以進行測試。 選取頂端導覽中的 [測試]  。 將測試語句 (例如「關閉燈光」) 輸入到 [互動測試] 窗格中，然後按下 Enter 鍵。 

```
Turn off the lights
```

檢查最高得分意圖是否對應至您對於每個測試語句所預期的意圖。

在此範例中，「關閉燈光」會正確識別為 "HomeAutomation.TurnOff" 的最高得分意圖。

[![已醒目提示語句的 測試 面板螢幕擷取畫面](media/luis-quickstart-new-app/test.png "已醒目提示語句的 測試 面板螢幕擷取畫面")](media/luis-quickstart-new-app/test.png)


再次選取 [測試]  以摺疊測試窗格。 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>發佈應用程式以取得端點 URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的語句來查詢端點

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. 移至位址中的 URL 結尾並輸入 `turn off the living room light`，然後按 Enter。 瀏覽器會顯示 HTTP 端點的 JSON 回應。

    [![使用 JSON 結果偵測 TurnOff 意圖的瀏覽器螢幕擷取畫面](media/luis-quickstart-new-app/turn-off-living-room.png "使用 JSON 結果偵測 TurnOff 意圖的瀏覽器螢幕擷取畫面")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

您可以從程式碼呼叫端點：

> [!div class="nextstepaction"]
> [使用程式碼呼叫 LUIS 端點](luis-get-started-cs-get-intent.md)
