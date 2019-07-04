---
title: 組成單位剖析 - 語言分析 API
titlesuffix: Azure Cognitive Services
description: 了解組成單位剖析 (也稱為「片語結構剖析」) 如何識別文字中的片語。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 7611f5f16111b5d8b0d2d293750f658125e50837
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60535425"
---
# <a name="constituency-parsing"></a>組成單位剖析

> [!IMPORTANT]
> 語言分析預覽已在 2018 年 8 月 9 日解除委任。 我們建議使用 [Azure Machine Learning 文字分析模組](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)來進行文字處理和分析。

組成單位剖析 (也稱為「片語結構剖析」) 的目標是要識別文字中的片語。
這對於從文字中擷取資訊很有幫助。
客戶或許會想尋找一大篇文字中的功能名稱或重要片語，以及查看每個這類片語周圍的修飾詞和動作。

## <a name="phrases"></a>片語

對語言學家來說，「片語」  不只是字組序列。
想要成為片語，字組群組必須聚集在一起進而在句子中扮演特定角色。
該字組群組可移動到一起，也可以作為整體來加以取代，而且句子應保持流暢且符合文法。

請看下列句子

> I want to find a new hybrid automobile with Bluetooth.

這個句子包含名詞片語：「a new hybrid automobile with Bluetooth」。
我們如何知道這是片語？
我們可以將這個句子改寫 (使其有點詩意)，讓這一個片語整個往前移：

> A new hybrid automobile with Bluetooth I want to find.

或者，我們可以將該片語替換成有類似作用和意思的片語，像是「a fancy new car」：

> I want to find a fancy new car.

如果我們沒這麼做，而是挑選不同的字組子集，這些替換工作就會導致奇怪或令人看不懂的句子。
請看當我們把「find a new」往前移時，會發生什麼情況：

> Find a new I want to hybrid automobile with Bluetooth.

其結果會讓人很難看懂。

剖析器的目標是要尋找所有這類片語。
有趣的是，在自然語言中，片語往往會彼此嵌套。
這些片語的自然表示法是樹狀結構，如下所示：

![樹狀結構](./Images/tree.png)

在此樹狀結構中，標示為「NP」的分支是名詞片語。
這類片語有好幾個：「I」  、「a new hybrid automobile」  、「Bluetooth」  和「a new hybrid automobile with Bluetooth」  。

## <a name="phrase-types"></a>片語類型

| ThisAddIn | 描述 | 範例 |
|-------|-------------|---------|
|ADJP   | 形容詞片語 | 「so rude」 |
|ADVP   | 副詞片語 | 「clear through」 |
|CONJP  | 連接詞片語 | 「as well as」 |
|FRAG   | 片段，用於不完整或零碎的輸入 | 「Highly recommended...」 |
|INTJ   | 感嘆詞 | 「Hooray」 |
|LST    | 清單標記，包括標點符號 | 「#4)」 |
|NAC    | 非組成單位，用來表示非組成單位片語的範圍 |  「you get things and for a good deal」中的「and for a good deal」 |
|NP | 名詞片語 | 「a tasty potato pancake」 |
|NX | 用於某些複雜的 NP 來標示開頭| |
|PP | 介係詞片語| 「in the pool」 |
|PRN    | 作為插入語的括號| 「(so called)」 |
|PRT    | 小品詞| 「ripped out」中的「out」 |
|QP | 名詞片語內的數量片語 (亦即，複雜的量值/數量)| 「around $75」 |
|RRC    | 縮略關係子句。| 「many issues still unresolved」中的「still unresolved」 |
|S  | 句子或子句。 | 「This is a sentence.」
|SBAR   | 從屬子句，通常由從屬連接詞導入 | 「I looked around as I left.」中的「as I left」|
|SBARQ  | 由 wh- 字組或片語導入的直接問句 | 「What was the point?」 |
|SINV   | 倒裝陳述句 | 「At no time were they aware.」 (請注意正常主詞「they」是如何移動到動詞「were」之後的) |
|SQ | 倒裝是非問句，或是 wh- 問句的主要子句 | 「Did they get the car?」 |
|UCP    | 非對等片語| 「small and with bugs」(請注意形容詞片語和介係詞片語是如何透過「and」來連結的)|
|VP | 動詞片語 | 「ran into the woods」 |
|WHADJP | Wh- 形容詞片語 | 「how uncomfortable」 |
|WHADVP | Wh- 副詞片語| 「when」 |
|WHNP   | Wh- 名詞片語| 「which potato」、「how much soup」|
|WHPP   | Wh- 介係詞片語| 「in which country」|
|X  | 不明、不確定或無法加上括弧。| 「the... the soup」中的第一個「the」 |


## <a name="specification"></a>規格

這裡的樹狀結構使用來自 [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) 的 S-表達式。
