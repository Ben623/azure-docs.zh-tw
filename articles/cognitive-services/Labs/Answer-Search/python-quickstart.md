---
title: 快速入門：專案答案搜尋 (Python)
titlesuffix: Azure Cognitive Services
description: 開始使用專案答案搜尋的 Python 範例。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: c35bf9649a0a22f3488c45d1f4f8729e211e0ddb
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707089"
---
# <a name="quickstart-project-answer-search-with-python"></a>快速入門：使用 Python 進行專案答案搜尋

下列 Python 範例會建立並傳送「直布羅陀巨巖」相關資訊的要求。

## <a name="prerequisites"></a>必要條件

取得免費試用版[認知服務實驗室](https://labs.cognitive.microsoft.com/en-us/project-answer-search)的存取金鑰

此範例使用 Python 3.6.4

## <a name="code-scenario"></a>程式碼案例 

下列程式碼會建立 URL 預覽。
其實作的步驟如下：
1. 宣告變數以依主機及路徑指定端點。
2. 指定要預覽的查詢 URL，並新增查詢參數。  
3. 設定查詢參數。
4. 定義建立要求的搜尋函式，並新增 *Ocp-Apim-Subscription-Key* 標頭。
5. 設定 *Ocp-Apim-Subscription-Key* 標頭。 
6. 進行連線，並傳送要求。
7. 列印 JSON 結果。

此示範的完整程式碼如下：

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```
## <a name="next-steps"></a>後續步驟
- [C# 快速入門](c-sharp-quickstart.md)
- [Java 快速入門](java-quickstart.md)
- [Node 快速入門](node-quickstart.md)
