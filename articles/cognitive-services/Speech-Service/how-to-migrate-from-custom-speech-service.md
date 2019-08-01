---
title: 從自訂語音服務移轉至語音服務
titleSuffix: Azure Cognitive Services
description: 「自訂語音服務」現在已是「語音服務」的一部分。 切換至「語音服務」即可享有最新品質與功能更新的好處。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562764"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>從自訂語音服務移轉至語音服務

使用本文將應用程式從「自訂語音服務」移轉至「語音服務」。

「自訂語音服務」現在已是「語音服務」的一部分。 切換至語音服務, 以受益于最新的品質和功能更新。

## <a name="migration-for-new-customers"></a>新客戶的移轉

計價模式更為簡單，並針對語音服務使用以小時為基礎的計價模式。  

1. 在提供您應用程式的每個區域中建立 Azure 資源。 Azure 資源名稱為「語音」  。 您可以將單一 Azure 資源用於相同區域中的下列服務，而不建立個別的資源：

    * 語音轉換文字
    * 自訂語音轉換文字
    * 文字轉換語音
    * 語音翻譯

2. 下載[語音 SDK](speech-sdk.md)。

3. 依照快速入門指南和 SDK 範例以使用正確的 API。 如果您使用 REST API，則也必須使用正確的端點和資源索引鍵。

4. 更新用戶端應用程式以使用語音服務和 Api。

## <a name="migration-for-existing-customers"></a>現有客戶的移轉

在語音服務入口網站上, 將您現有的資源金鑰遷移至語音服務。 請使用下列步驟：

> [!NOTE]
> 資源索引鍵只能在相同的區域內進行移轉。

1. 登入 [cris.ai](https://cris.ai/Home/CustomSpeech) \(英文\) 入口網站，然後選取右上方功能表中的訂用帳戶。

2. 選取 [Migrate selected subscription] \(移轉選取的訂用帳戶\)  。

3. 在文字方塊中輸入訂用帳戶金鑰，然後選取 [Migrate]  \(移轉\)。

## <a name="next-steps"></a>後續步驟

* [免費試用語音服務](get-started.md)。
* 了解[語音轉換文字](./speech-to-text.md)概念。

## <a name="see-also"></a>另請參閱

* [什麼是語音服務](overview.md)
* [語音服務和語音 SDK 檔](speech-sdk.md#get-the-sdk)
