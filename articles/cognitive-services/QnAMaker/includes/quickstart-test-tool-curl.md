---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424201"
---
這個以 cURL 為基礎的快速入門會逐步引導您從知識庫取得答案。

## <a name="prerequisites"></a>必要條件

* 最新 [**cURL**](https://curl.haxx.se/)。
* 您必須具備 [QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)及[包含問題與答案的知識庫](../Tutorials/create-publish-query-in-portal.md)。

## <a name="publish-to-get-endpoint"></a>發佈以取得端點

當您準備好從知識庫產生問題的答案時，請[發佈](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)您的知識庫。

## <a name="use-production-endpoint-with-curl"></a>搭配 cURL 使用生產端點

發佈知識庫時，[發佈]  頁面會顯示用來產生答案的 HTTP 要求設定。 [CURL]  索引標籤會顯示從命令列工具 ([CURL](https://www.getpostman.com)) 產生答案所需的設定。

[![發佈結果](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

若要使用 CURL 產生答案，請完成下列步驟：

1. 複製 [CURL] 索引標籤中的文字。 
1. 開啟命令列或終端機，然後貼上文字。
1. 編輯與您的知識庫相關的問題。 請小心不要移除問題周圍包含的 JSON。
1. 輸入命令。 
1. 回應包含答案的相關資訊。 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>搭配 cURL 使用暫存端點

如果您想要從暫存端點取得解答，請使用 `isTest` 本文屬性。

```json
isTest:true
```


