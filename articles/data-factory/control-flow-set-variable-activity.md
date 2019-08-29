---
title: Azure Data Factory 中的設定變數活動 | Microsoft Docs
description: 了解如何使用「設定變數」活動來設定在 Data Factory 管線中定義的現有變數值
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: cfe6dd63234a7750fe01614d6f1b38bb7cce1adb
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142433"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory 中的設定變數活動

使用「設定變數」活動，為定義於 Data Factory 管線中的「字串」、「布林」或「陣列」類型設定現有變數值。

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 必要項
-------- | ----------- | --------
name | 管線中的活動名稱 | 是
description | 說明活動用途的文字 | 否
Type | 活動類型是 SetVariable | 是
value | 用來設定指定變數的字串常值或運算式物件值 | 是
variableName | 此活動所將設定的變數名稱 | 是


## <a name="next-steps"></a>後續步驟
深入了解 Data Factory 所支援的相關控制流程活動： 

- [附加變數活動](control-flow-append-variable-activity.md)
