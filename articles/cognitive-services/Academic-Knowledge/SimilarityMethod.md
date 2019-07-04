---
title: 相似度方法 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 使用相似度方法計算兩個字串的學術相似度。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 7f692c08f8af322bf7e6ab576e2e6f516594a6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336512"
---
# <a name="similarity-method"></a>相似度方法

**相似度** REST API 用來計算兩個字串之間的學術相似度。 
<br>

**REST 端點：**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>要求參數

參數        |資料類型      |必要項 | 描述
----------|----------|----------|------------
**s1**        |字串   |是  |要進行比較的字串*
**s2**        |字串   |是  |要進行比較的字串*

<sub> *要比較的字串具有的最大長度為 1 MB。</sub>
<br>

## <a name="response"></a>Response

名稱 | 描述
--------|---------
**SimilarityScore**        |浮點值，表示 s1 和 s2 的餘弦相似度，具有較接近 1.0 的值表示較為相似，而較接近 -1.0 的值則表示較不相似

<br>

## <a name="successerror-conditions"></a>成功/錯誤條件

HTTP 狀態 | `Reason` | Response
-----------|----------|--------
**200**         |成功 | 浮點數
**400**         | 錯誤的要求或要求無效 | 錯誤訊息      
**500**         |內部伺服器錯誤 | 錯誤訊息
**逾時**     | 要求逾時。  | 錯誤訊息

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>範例：計算兩個部份摘要的相似度
#### <a name="request"></a>要求：
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
在此範例中，我們會使用**相似度** API 來產生兩個部份摘要之間的相似度分數。
#### <a name="response"></a>回應：
```
0.520
```
#### <a name="remarks"></a>備註：
相似度分數的決定方式是透過字組內嵌來評估學術概念。 在此範例中，0.52 表示兩個部份摘要有點類似。
<br>
