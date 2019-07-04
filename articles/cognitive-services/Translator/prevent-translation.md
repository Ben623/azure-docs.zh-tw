---
title: 防止翻譯內容 - Translator Text API
titlesuffix: Azure Cognitive Services
description: 使用 Translator Text API 防止翻譯內容。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 02c4e90879b81e29f3972d262f36d5c6b6d8e841
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448276"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>如何使用 Translator Text API 防止翻譯內容

Translator Text API 可讓您標記內容，使其不要翻譯。 例如，您可以標記程式碼、品牌名稱，或當地語系化後沒有意義的字組/片語。

## <a name="methods-for-preventing-translation"></a>防止翻譯的方法
1. 逸出至 Twitter 標籤 @somethingtopassthrough 或 #somethingtopassthrough。 在翻譯後取消逸出。

2. 使用 `notranslate` 標記您的內容。

   範例：

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. 使用[動態字典](dynamic-dictionary.md)來規定特定翻譯。

4. 請勿將字串傳遞至 Translator Text API 進行翻譯。

5. 自訂翻譯：使用[自訂翻譯中的字典](custom-translator/what-is-dictionary.md)來規定 100% 機率的片語翻譯。


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [避免在 Translator API 呼叫中翻譯](reference/v3-0-translate.md)
