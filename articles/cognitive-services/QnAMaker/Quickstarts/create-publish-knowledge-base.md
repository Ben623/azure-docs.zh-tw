---
title: 快速入門：建立、訓練及發佈知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 您可以從自己的內容 (例如常見問題集或產品手冊) 建立 QnA Maker 知識庫 (KB)。 此範例中的 QnA Maker 知識庫是從簡單的常見問題集網頁建立而來，以回答 BitLocker 金鑰復原問題。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: aab64822730531acdcf5f3d91ed8bf028ce7cfd4
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971960"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>快速入門：建立、訓練及發佈您的 QnA Maker 知識庫

您可以從自己的內容 (例如常見問題集或產品手冊) 建立 QnA Maker 知識庫 (KB)。 本文包含從簡單的常見問題集網頁建立 QnA Maker 知識庫的範例，以回答 BitLocker 金鑰復原問題。

加入閒聊特質，讓您的知識更貼近您的用戶。

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>必要條件

> [!div class="checklist"]
> * 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-new-qna-maker-knowledge-base"></a>建立新的 QnA Maker 知識庫

1. 利用您的 Azure 認證登入 [QnAMaker.ai](https://QnAMaker.ai) 入口網站。

1. 在 QnA Maker 入口網站上，選取 [建立知識庫]  。

1. 在 [建立]  頁面上，選取 [建立 QnA 服務]  。 系統會將您導向 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以在您的訂用帳戶中設定 QnA Maker 服務。 

1. 在 QnA Maker 入口網站中，從下拉式清單選取您的 QnA Maker 服務。 如果您已建立新的 QnA Maker 服務，請務必重新整理頁面。

   ![選取 QnA Maker 服務知識庫的螢幕擷取畫面](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 將您的知識庫命名為**我的範例 QnA KB**。

1. 新增範例 Word 作為 URL： 

    `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`

1. 選取 `+ Add URL`。

1. 將「專業」  **閒聊**新增至您的 KB。 

1. 選取 [建立您的 KB]  。

    擷取程序需要幾分鐘的時間來讀取文件並找出問題和回答。

    在 QnA Maker 成功建立知識庫之後，[知識庫]  頁面隨即開啟。 您可以在此頁面上編輯知識庫的內容。

## <a name="add-a-new-question-and-answer-set"></a>新增問答集

1. 在 QnA Maker 入口網站的 [編輯]  頁面上，選取 [新增 QnA 組]  。
1. 新增下列問題： 

    `How many Azure services are used by a knowledge base?`

1. 新增使用 _markdown_ 設定格式的答案：

    ` * Azure QnA Maker service\n* Azure Search\n* Azure web app\n* Azure app plan`

    ![ 以文字型式新增問題與使用 markdown 設定格式的答案。](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    markdown 符號 `*` 用於表示項目符號。 `\n` 用於表示新行。  

    [編輯]  頁面會顯示 markdown。 當您稍後使用 [測試]  面板時，您將會看到 markdown 正確顯示。 

## <a name="save-and-train"></a>儲存並定型

在右上方，選取 [儲存並定型]  來儲存您的編輯內容並將 QnA Maker 模型定型。 除非儲存編輯內容，否則不會保留。

## <a name="test-the-knowledge-base"></a>測試知識庫

1. 在 QnA Maker 入口網站的右上方，選取 [測試]  來測試您所做的變更是否已生效。 
1. 在文字方塊中輸入範例使用者查詢。 

    `How many Azure services are used by a knowledge base?`  

    ![ 在文字方塊中輸入範例使用者查詢。 ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. 選取 [檢查]  ，更詳細地檢查回應。 測試視窗是用來測試您對知識庫所做的變更，然後加以發佈。

1. 再次選取 [測試]  以關閉 [測試]  面板。

## <a name="publish-the-knowledge-base"></a>發佈知識庫

當您發佈知識庫時，您知識庫的內容會從 `test` 索引移到 Azure 搜尋服務中的 `prod` 索引。

![移動您的知識庫內容的螢幕擷取畫面](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. 在 QnA Maker 入口網站中，選取 [發佈]  。 若要接著確認，請選取頁面上的 [發佈]  。

    QnA Maker 服務現在已成功發佈。 您可以在您的應用程式或 Bot 程式碼中使用此端點。

    ![發佈成功的螢幕擷取畫面](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>建立 Bot

發佈之後，您可以從 [發佈]  頁面建立 Bot： 

* 您可以快速建立數個 Bot，讓全部 Bot 指向相同知識庫中個別 Bot 適用的不同區域或定價方案。 
* 如果您想讓知識庫只有一個 Bot，可使用**在 Azure 入口網站中檢視 Bot** 連結，來檢視您目前的 Bot 清單。 

當您變更知識庫並重新發佈時，不需要對 Bot 採取進一步的動作。 其已設定為與知識庫搭配使用，而且會與知識庫未來的所有變更搭配運作。 每次發佈知識庫後，與之連線的所有 Bot 就會自動更新。

1. 在 QnA Maker 入口網站的 [發佈]  頁面上，選取 [建立 Bot]  。 只有在發佈知識庫後，此按鈕才會出現。

    ![建立 Bot 的螢幕擷取畫面](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Azure 入口網站會在新的瀏覽器索引標籤中開啟，其中會包含 Azure Bot 服務的建立頁面。 設定 Azure Bot 服務。 
    
    * 建立 Bot 時，請勿在 Azure 入口網站中變更下列設定。 這些是為您現有知識庫預先填入的內容： 
        * QnA 驗證金鑰
        * App Service 方案和位置
    * Bot 與 QnA Maker 可共用 Web 應用程式服務方案，但不能共用 Web 應用程式。 這表示 Bot 的**應用程式名稱**必須與 QnA Maker 服務的應用程式名稱不同。 

1. 建立 Bot 之後，請開啟 [Bot 服務]  資源。 
1. 在 [Bot 管理]  下，選取 [在網路聊天中測試]  。
1. 在 [輸入您的訊息]  聊天提示中，輸入：

    `Azure services?`

    聊天 Bot 會使用來自您知識庫的答案來回應。 

    ![在測試網頁聊天中輸入使用者查詢。](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>清除資源

在 Azure 入口網站中清除 QnA Maker 與 Bot 架構資源。 

## <a name="next-steps"></a>後續步驟

其他資訊：

* [ Markdown 格式](../concepts/data-sources-supported.md)
* [測試您的 markdown](../concepts/data-sources-supported.md#testing-your-markdown)
* QnA Maker [資料來源](../Concepts/data-sources-supported.md)。 
* [Bot 資源組態設定](../tutorials/create-qna-bot.md)。

> [!div class="nextstepaction"]
> [新增具有中繼資料的問題](add-question-metadata-portal.md)

