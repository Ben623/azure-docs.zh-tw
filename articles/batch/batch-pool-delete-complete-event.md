---
title: Azure Batch 集區刪除完成事件 | Microsoft Docs
description: Batch 集區刪除完成事件的參考。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: fd32554866d1e2130fd0833adc1b286fb6bc07a5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323236"
---
# <a name="pool-delete-complete-event"></a>集區刪除完成事件

 集區刪除作業完成時，就會發出此事件。

 下列事件顯示集區刪除完成事件內文。

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|id|字串|集區識別碼。|
|startTime|Datetime|集區刪除開始時間。|
|endTime|DateTime|集區刪除完成時間。|

## <a name="remarks"></a>備註
如需集區調整大小作業狀態與錯誤碼的詳細資訊，請參閱[將集區自帳戶中刪除](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account) (英文)