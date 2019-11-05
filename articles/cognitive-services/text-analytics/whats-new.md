---
title: 文字分析 API 的新功能
titlesuffix: Text Analytics - Azure Cognitive Services
description: 瞭解文字分析服務的新發展
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 44ef6fb118be4d1110a693faded6c57bc8b4e2fd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499948"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文字分析 API 有哪些新功能？

文字分析 API 會持續更新。 為了讓您隨時掌握最新的開發，這篇文章為您提供新版本和功能的相關資訊。

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>命名實體辨識 v3 公開預覽-2019 年10月

下一版的命名實體辨識（NER）現已開放公開預覽，並提供在文字中找到之實體的展開偵測和分類。 它提供：

* 識別下列新的實體類型：
    * 電話號碼
    * IP 位址

* 識別個人資訊實體類型的新端點（僅限英文版）
* 實體辨識和實體連結的個別端點。

實體連結支援英文和西班牙文。 NER 語言支援會因實體類型而異。 如需詳細資訊，請參閱下面的連結。 

> [!div class="nextstepaction"]
> [深入瞭解命名實體辨識 v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>情感分析 v3 公開預覽-2019 年10月

下一版的情感分析現已開放公開預覽，並大幅改善了 API 文字分類和計分的精確度和詳細資料。 它還提供：

* 自動標記文字中的不同情緒。
* 在檔和句子層級上情感分析和輸出。 

它支援英文（`en`）、日文（`ja`）、簡體中文（`zh-Hans`）、繁體中文（`zh-Hant`）、法文（`fr`）、義大利文（`it`）、西班牙文（`es`）、荷蘭文（`nl`）、葡萄牙文（`pt`）和德文（`de`），和適用于下欄區域： `Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe`和 `West US 2`。

> [!div class="nextstepaction"]
> [深入瞭解情感分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>後續步驟

* [什麼是文字分析 API？](overview.md)  
* [使用者案例範例](text-analytics-user-scenarios.md)
* [情感分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [語言偵測](how-tos/text-analytics-how-to-language-detection.md)
* [實體辨識](how-tos/text-analytics-how-to-entity-linking.md)
* [關鍵片語擷取](how-tos/text-analytics-how-to-keyword-extraction.md)
