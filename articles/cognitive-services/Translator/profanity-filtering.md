---
title: 粗話篩選功能 - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 使用 [不雅內容篩選] 來判斷您在 Azure 認知服務翻譯工具文字 API 的文字中翻譯的不雅內容層級。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836236"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>使用翻譯工具文字 API 新增粗話篩選功能

Translator 服務通常會在翻譯中保留存在於來源中的粗話。 粗話程度和導致單字不雅的內容在文化特性之間各異。 因此，粗話程度在目標語言中可能增強，也可能減弱。

如果您想要避免在翻譯中看到粗話 (即使來源文字就有粗話)，可使用 Translate() 方法中可用的粗話篩選選項。 此選項可讓您選擇是要看到粗話被刪除、以適當的標籤標記起來，還是不採取任何動作。

Translate() 方法會接受 “options” 參數，其中包含了新的元素 “ProfanityAction”。 可接受的 ProfanityAction 值包括 “NoAction”、“Marked” 及 “Deleted”。

## <a name="accepted-values-of-profanityaction-and-examples"></a>可接受的 ProfanityAction 值和範例
|ProfanityAction 值 | 動作 | 範例：來源 - 日文 | 範例：目標 - 英文|
| :---|:---|:---|:---|
| NoAction | 預設值。 與未設定此選項相同。 粗話會從來源傳遞到目標。 | 彼は変態です。 | He is a jerk. |
| Marked | 不雅的單字會括上 XML 標籤 \<profanity> ... \</profanity>。 | 彼は変態です。 | He is a \<profanity>jerk\</profanity>. |
| Deleted | 將會從輸出中移除不雅單字，而不予以取代。 | 彼は。 | He is a. |

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用 Translator API 呼叫來套用粗話篩選功能](reference/v3-0-translate.md)
