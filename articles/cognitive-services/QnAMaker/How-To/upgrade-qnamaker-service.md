---
title: 升級 QnA Maker 服務 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 共用或升級，QnA Maker 服務才能管理更好的資源。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 2fdbb245f838d92e84d1247faa610a2f1a66c532
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439756"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>共用或升級您的 QnA Maker 服務
共用或升級，QnA Maker 服務才能管理更好的資源。 

您可以選擇在初始建立之後升級 QnA Maker 堆疊的個別元件。 請在[這裡](https://aka.ms/qnamaker-docs-capacity)參閱相依元件和 SKU 選取項目的詳細資料。

## <a name="share-existing-services-with-qna-maker"></a>QnA Maker 與共用現有的服務

QnA Maker 會建立數個 Azure 資源。 為了減少管理，並受益於成本共用，請使用下表，了解哪些可以和無法共用：

|服務|共用|
|--|--|
|認知服務|X|
|App Service 方案|✔|
|App Service|X|
|Application Insights|✔|
|搜尋服務|✔|

## <a name="upgrade-qna-maker-management-sku"></a>升級 QnA Maker 管理 SKU

如果需要超過您目前層次的更多問題和答案放入您的知識庫，請將 QnA Maker 服務定價層升級。 

若要升級 QnA Maker 管理 SKU：

1. 在 Azure 入口網站中移至 QnA Maker 資源，並選取 [定價層]  。

    ![QnA Maker 資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. 選擇適當的 SKU，並按下 [選取]  。

    ![QnA Maker 價格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>升級 App Service

 當您的知識庫需要為來自用戶端應用程式的更多要求提供服務時，請將 App Service 定價層升級。

您可以[相應增加](https://docs.microsoft.com/azure/app-service/web-sites-scale)或相應減少應用程式服務。

1. 移至 Azure 入口網站中應用程式服務資源，並且視需要選取 [相應增加]  或 [相應減少]  選項。

    ![QnA Maker 應用程式服務規模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>升級 Azure 搜尋服務

如果您打算有許多知識庫，請將 Azure 搜尋服務定價層升級。 

目前無法執行 Azure 搜尋服務 SKU 的就地升級。 不過，您可以使用所需的 SKU 建立新的 Azure 搜尋服務資源，並將資料還原到新的資源，然後再將新的資源連結至 QnA Maker 堆疊。

1. 在 Azure 入口網站中建立新的 Azure 搜尋服務資源，然後選擇所需的 SKU。

    ![QnA Maker Azure 搜尋服務資源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. 從原始的 Azure 搜尋服務資源將索引還原到新的 Azure 搜尋服務資源。 請在[這裡](https://github.com/pchoudhari/QnAMakerBackupRestore)參閱備份還原範例程式碼。

3. 還原資料之後，請移至新 Azure 搜尋服務資源，並選取 [金鑰]  ，然後記下**名稱**和**管理金鑰**。

    ![QnA Maker Azure 搜尋服務索引鍵](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. 若要將新的 Azure 搜尋服務資源連結到 QnA 堆疊，請移至 QnA Maker 應用程式服務。

    ![QnA Maker 應用程式服務](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. 選取 [應用程式設定]  ，並取代步驟 3 個 [AzureSearchName]  和 [AzureSearchAdminKey]  欄位。

    ![QnA Maker 應用程式服務設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. 重新啟動 App Service。

    ![QnA Maker 應用程式服務重新啟動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 QnA Maker API](../Quickstarts/csharp.md) (英文)
