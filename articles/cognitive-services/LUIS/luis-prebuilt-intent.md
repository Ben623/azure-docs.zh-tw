---
title: 預先建置的意圖
titleSuffix: Azure Cognitive Services
description: LUIS 包含一組預先建置的意圖，可用來快速新增常見的交談式使用者情節。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 8e11b6d5a9c9df10e789c8e0be3a90d94ecf3d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505655"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>新增常見意圖的預先建置意圖 

LUIS 包含一組來自預先建置定義域的預先建置意圖，可用來快速新增常見的意圖和語句。 這是一個既快速又簡便的方式，讓您無須為功能設計模型，即可為您的交談式用戶端應用程式新增功能。 

## <a name="add-a-prebuilt-intent"></a>新增預先建置的意圖

1. 在 [My Apps] \(我的應用程式\)  頁面上，選取您的應用程式。 這會讓您的應用程式開啟至應用程式的 [Build] \(組建\)  區段。 

1. 在 [Intents] \(意圖\)  頁面上，從意圖清單上方的工具列中選取 [Add prebuilt intent] \(新增預先建置的意圖\)  。 

1. 從快顯對話方塊中選取 [Utilities.Cancel]  意圖。 

    ![新增預先建置的意圖](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. 選取 [Done] \(完成\)  按鈕。

## <a name="train-and-test"></a>定型和測試

1. 新增意圖之後，選取右上方工具列中的 [Train] \(定型\)  來進行應用程式定型。 

1. 選取右側工具列中的 [Test] \(測試\)  來測試新的意圖。 

1. 在文字方塊中，輸入表達取消的語句：

    |測試語句|預測分數|
    |--|:--|
    |我想要取消我的航班。|0.67|
    |取消購買。|0.52|
    |取消會議。|0.56|

    ![測試預先建置的意圖](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [預先建置實體](./luis-prebuilt-entities.md)
