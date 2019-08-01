---
title: 快速入門：專案答案搜尋 (Node)
description: 開始使用專案答案搜尋與 Node。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 21dda323cf465f56fb3f43160dd04c27a81d5590
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698082"
---
# <a name="quickstart-project-answer-search-with-node"></a>快速入門：使用 Node 進行專案答案搜尋

下列 Node 範例建立 Yosemite 國家公園相關資訊的查詢。

## <a name="prerequisites"></a>必要條件

取得免費試用版[認知服務實驗室](https://labs.cognitive.microsoft.com/en-us/project-answer-search)的存取金鑰

此範例使用 Node v8.9.4

## <a name="code-scenario"></a>程式碼案例 

下列程式碼取得答案。
實際進行步驟如下：
1. 宣告變數以依主機及路徑指定端點。
2. 指定要預覽的查詢 URL，並加入查詢參數。  
3. 建立回應的處理常式函式。
4. 定義建立要求的搜尋函式，並加入 *Ocp-Apim-Subscription-Key* 標頭。
5. 執行搜尋函式。 

此示範的完整程式碼如下：

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt= + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>後續步驟
- [C# 範例程式碼](c-sharp-quickstart.md)
- [Java 快速入門](java-quickstart.md)
- [Python 快速入門](python-quickstart.md)
