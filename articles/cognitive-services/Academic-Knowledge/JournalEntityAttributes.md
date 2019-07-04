---
title: 期刊實體屬性 - 學術知識 API
titlesuffix: Azure Cognitive Services
description: 了解您可以在認知服務的學術知識 API 中搭配期刊實體使用的屬性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ffb159dc684b4b6663dcb966706d4745ab88a403
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61337800"
---
# <a name="journal-entity"></a>期刊實體

<sub> *下列屬性專屬於期刊實體。(Ty = '2') </sub>

名稱    |描述                            |類型       | 作業
------- | ------------------------------------- | --------- | ----------------------------
Id      |實體識別碼                              |Int64      |Equals
DJN     |期刊標準化名稱                |String     |None
JN      |期刊顯示名稱                   |字串     |Equals
CC      |期刊引用總數           |Int32      |None  
ECC     |期刊引用預估總數 |Int32      |None
