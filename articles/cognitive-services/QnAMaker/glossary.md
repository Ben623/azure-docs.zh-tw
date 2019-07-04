---
title: 詞彙 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 服務有許多來自機器學習和自然語言處理的新詞彙，以及服務特定的詞彙。 這份清單將協助您了解這些詞彙。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c6b7e4ca2acc1416e19fc8b70f57aed82afa4e1e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446539"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>QnA Maker 知識庫和服務的字彙

## <a name="qna-maker-service"></a>QnA Maker 服務
必須要具備 QnA Maker 服務，方可開始使用 QnA Maker。 購買 QnA Maker 層，即可在您的 Azure 訂用帳戶中設定資源，以便建立和管理您的知識庫。 每個 QnA Maker 使用者帳戶都可以在自己的 Azure 訂用帳戶中建立多個 QnA Maker 服務。

## <a name="knowledge-base"></a>知識庫
知識庫是儲存以 QnA Maker 建立、維護和使用的問答的存放庫。 每個 QnA Maker 層均可用於多個知識庫。

## <a name="endpoint"></a>端點
提供知識庫內容的 REST 架構 HTTP 端點可以整合到您的應用程式，通常是聊天機器人。 

## <a name="test-knowledge-base"></a>測試知識庫
知識庫有兩種狀態：測試和發佈。 測試知識庫是正在編輯、儲存和測試回應正確性與完整性的版本。 對於測試知識庫所做的變更，不會影響到應用程式/聊天機器人的終端使用者。

## <a name="published-knowledge-base"></a>發佈的知識庫
知識庫有兩種狀態：測試和發佈。  發佈的知識庫是聊天機器人/應用程式所用的版本。 發佈知識庫的動作會將測試知識庫的內容放入發佈的知識庫版本。 因為發佈的知識庫是應用程式透過端點使用的版本，所以應該小心確保內容正確且經過完整測試。

## <a name="query"></a>查詢
使用者查詢是使用者或測試人員對於知識庫提出的問題。 查詢通常採用的是自然語言格式或表示問題的一些關鍵字。

## <a name="response"></a>Response
回應是根據最符合特定使用者查詢的回應從知識庫擷取的答案。

## <a name="confidence-score"></a>信賴分數
回應的信賴分數是介於 0 和 100 之間的數值，100 代表使用者查詢與知識庫中的問題之間的完全查詢相符，而且提供的回應對於特定的使用者查詢是正確適當的回應。 答案通常排序次序的信賴分數，則具有較高的信心分數擔任[預設回應](concepts/confidence-score.md#change-default-answer)。
