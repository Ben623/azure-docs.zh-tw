---
title: 什麼是 QnA Maker？
titleSuffix: Azure Cognitive Services
description: QnA Maker 是雲端式 API 服務，會對使用者的自然語言問題套用自訂的機器學習智慧以提供最佳解答。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: bfb6c5b7cc5a4bd1717fdd96f6d232cc269e702d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439579"
---
# <a name="what-is-qna-maker"></a>什麼是 QnA Maker？

QnA Maker 是一項雲端式 API 服務，可對您的資料建立交談式的問答層。 

QnA Maker 可讓您從半結構化內容建立知識庫 (KB)，例如常見問題集 (FAQ) URL、產品手冊、支援文件，和自訂問題與答案。 QnA Maker 服務會將使用者的自然語言問題與知識庫中各個 QnA 可能包含的最佳解答比對，以回答這些問題。

易於使用的 [Web 入口網站](https://qnamaker.ai)可讓您在沒有任何開發人員經驗的情況下，建立、管理和訓練和發佈服務。 服務發佈至端點後，聊天機器人等用戶端應用程式即可管理與使用者所進行的交談，以取得問題並回覆答案。 

![概觀](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>主要的 QnA Maker 處理程序

QnA Maker 可為您的資料提供兩項主要服務：

* **擷取**：結構化的問題-解答資料會擷取自結構化和半結構化[資料來源](../Concepts/data-sources-supported.md)，例如常見問題集和產品手冊。 此擷取會在[建立](https://aka.ms/qnamaker-docs-createkb) KB 時完成，或是在之後的編輯程序中完成。

* **比對**：一旦您的知識庫經過[訓練和測試](https://aka.ms/qnamaker-docs-trainkb)後，您就可以[發行](https://aka.ms/qnamaker-docs-publishkb)它。 這可對 QnA Maker 知識庫啟用端點，然後讓您在聊天機器人或用戶端應用程式中使用。 此端點接受使用者問題，而且會以知識庫中最佳的答案作為回應，其中會包含該組合的信賴分數。

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>QnA Maker 架構

QnA Maker 架構由下列兩個元件組成：

1. **QnA Maker 管理服務**：QnA Maker 知識庫的管理體驗，其中包含初始建立、更新、訓練及發佈。 這些活動可以透過[入口網站](https://qnamaker.ai)或[管理 API](https://go.microsoft.com/fwlink/?linkid=2092179) 來完成。 

2. **QnA Maker 資料和執行階段**：這會部署在指定區域中的 Azure 訂用帳戶。 您的 KB 內容儲存在 [Azure 搜尋服務](https://azure.microsoft.com/services/search/)中，而且端點會部署為[應用程式服務](https://azure.microsoft.com/services/app-service/)。 您也可以選擇部署 [Application Insights](https://azure.microsoft.com/services/application-insights/) 資源來進行分析。

![架構](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>服務要點

- 從知識庫透過完全**無程式碼**的體驗來[建立聊天機器人](../Quickstarts/create-publish-knowledge-base.md#create-a-bot)。
- **預測時不會有網路節流**。 針對裝載的服務支付費用，而非針對交易數目。 請參閱[定價頁面](https://aka.ms/qnamaker-docs-pricing)，以取得詳細資料。
- **視需要調整**。 為符合您案例的個別元件選擇適當的 SKU。 了解如何為您的 QnA Maker 服務[選擇容量](https://aka.ms/qnamaker-docs-capacity)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 QnA Maker 服務](../how-to/set-up-qnamaker-service-azure.md)
