---
title: 檢查自訂語音語音服務的資料品質
titleSuffix: Azure Cognitive Services
description: 「自訂語音」提供工具，可讓您以視覺化方式檢查模型的辨識品質，方法是比較音訊資料與對應的辨識結果。 您可以播放上傳的音訊，並判斷提供的辨識結果是否正確。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: cf0deffb6fe484f7e3ee38b95f285bb4b7cc809c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072567"
---
# <a name="inspect-custom-speech-data"></a>檢查自訂語音資料

> [!NOTE]
> 此頁面會假設您已閱讀[準備自訂語音的測試資料](how-to-custom-speech-test-data.md)，並已上傳資料集以進行檢查。

「自訂語音」提供工具，可讓您以視覺化方式檢查模型的辨識品質，方法是比較音訊資料與對應的辨識結果。 從[自訂語音入口網站](https://speech.microsoft.com/customspeech)，您可以播放上傳的音訊，並判斷提供的辨識結果是否正確。 這項工具可讓您快速檢查 Microsoft 的基準語音轉換文字模型或定型自訂模型的品質，而不需要轉譯任何音訊資料。

在本檔中，您將瞭解如何使用您先前上傳的定型資料，以視覺化方式檢查模型的品質。

在此頁面上，您將瞭解如何以視覺化方式檢查 Microsoft 的基準語音轉換文字模型和（或）您已定型的自訂模型品質。 您將使用您上傳到 [**資料**] 索引標籤的資料進行測試。

## <a name="create-a-test"></a>建立測試

請依照下列指示來建立測試：

1. 登入[自訂語音入口網站](https://speech.microsoft.com/customspeech)。
2. 流覽至**語音轉換文字 > 自訂語音 > 測試**。
3. 按一下 [**加入測試**]。
4. 選取 **[檢查品質（僅限音訊資料）** ]。 為測試指定名稱、描述，然後選取您的音訊資料集。
5. 選取最多兩個您想要測試的模型。
6. 按一下頁面底部的 [新增]。

成功建立測試之後，您可以並排比較模型。

## <a name="side-by-side-model-comparisons"></a>並存模型比較

當測試狀態為 [_成功_] 時，按一下 [測試專案名稱] 以查看測試的詳細資料。 此詳細資料頁面會列出資料集內的所有語句，指出兩個模型與所提交資料集之轉譯的辨識結果。

若要協助檢查並存比較，您可以切換各種錯誤類型，包括插入、刪除和替代。 藉由接聽音訊並比較每個資料行中的辨識結果（顯示人為標記的轉譯和兩個語音轉換文字模型的結果），您可以決定哪一個模型符合您的需求，以及需要改進的位置。

檢查品質測試很適合用來驗證語音辨識端點的品質是否足以用於應用程式。 如需精確度的目標量值（需要轉譯音訊），請遵循[評估精確度](how-to-custom-speech-evaluate-data.md)中的指示。

## <a name="next-steps"></a>後續步驟

- [評估您的資料](how-to-custom-speech-evaluate-data.md)
- [訓練您的模型](how-to-custom-speech-train-model.md)
- [部署您的模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

- [準備自訂語音的測試資料](how-to-custom-speech-test-data.md)
