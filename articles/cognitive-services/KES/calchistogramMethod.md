---
title: CalcHistogram 方法 - 知識探索服務 API
titlesuffix: Azure Cognitive Services
description: 了解如何知識探索服務 (KES) API 中使用 CalcHistogram 方法。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: aaa5b3a85c08f11d821557257de451b8ffc8a3fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814165"
---
# <a name="calchistogram-method"></a>CalcHistogram 方法
calcHistogram  方法會計算與結構化查詢運算式相符的物件，並計算其屬性值的分佈。

## <a name="request"></a>要求
`http://<host>/calchistogram?expr=<expr>[&options]` 

名稱|值|描述
----|-----|-----------
expr | 文字字串 | 結構化查詢運算式，用來指定用於計算長條圖的索引實體。
屬性 | 文字字串 (預設值 = "") | 回應中所要包含屬性的逗號分隔清單。
count   | 數字 (預設值 = 10) | 要傳回的結果數目。
Offset  | 數字 (預設值 = 0) | 要傳回的第一個結果索引。

## <a name="response-json"></a>回應 (JSON)
JSONPath | 描述
----|----
$.expr | 要求中的 expr  參數。
$.num_entities | 相符實體的總數。
$.histograms |  長條圖的陣列，每個所要求的屬性各有一個長條圖。
$.histograms[\*].attribute | 用於計算長條圖的屬性名稱。
$.histograms[\*].distinct_values | 此屬性的相符實體當中的相異值數目。
$.histograms[\*].total_count | 此屬性的相符實體當中的值執行個體總數。
$.histograms[\*].histogram | 此屬性的長條圖資料。
$.histograms[\*].histogram[\*].value | 屬性值。
$.histograms[\*].histogram[\*].logprob  | 這個屬性值相符實體的總計自然對數機率。
$.histograms[\*].histogram[\*].count    | 這個屬性值的相符實體數目。
$.aborted | 如果要求逾時，則為 true。

### <a name="example"></a>範例
在學術刊物的範例中，下列命令會依年度及關鍵字計算某位特定作者自 2013 年之後所發表刊物的計數長條圖：

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

回應中會指出有 37 篇符合查詢運算式的論文。  Year  屬性有 3 個相異值，自 2013 年後的每一年各一個值。  3 個相異值的總計論文計數為 37。  對於每個 Year  ，長條圖會顯示值、總計自然對數可能性及相符實體計數。     

「關鍵字」  的長條圖顯示有 34 個不同的關鍵字。 因為一篇論文可能與多個關鍵字相關聯，所以總計數 (53) 可以大於相符的實體數目。  雖然有 34 個相異值，但回應只包含前 4 個值，因為 "count=4" 參數。

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
