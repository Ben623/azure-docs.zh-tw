---
title: Azure Batch 集區調整大小完成事件 | Microsoft Docs
description: Batch 集區調整大小完成事件的參考。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: a11dec8998a77153cd10b6caf72f5885c69b70c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094777"
---
# <a name="pool-resize-complete-event"></a>集區調整大小完成事件

 集區調整大小完成或失敗時，就會發出此事件。

 下列範例顯示大小增加且成功完成的集區其集區調整大小完成事件內文。

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|ID|String|集區識別碼。|
|nodeDeallocationOption|String|指定當集區大小一直減少時，會自集區中移除節點。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 終止執行中工作並重新排入佇列。 當作業啟用時，工作將再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **terminate** – 終止執行中工作。 工作將不會再次執行。 一旦工作終止，隨即移除節點。<br /><br /> **taskcompletion** – 允許目前執行中工作完成。 等待時不排程任何新的工作。 所有工作完成時，即移除節點。<br /><br /> **Retaineddata** - 允許目前執行中工作完成，然後等待所有工作資料保留期到期。 等待時不排程任何新的工作。 當所有工作保留期到期時即移除節點。<br /><br /> 預設值為 requeue。<br /><br /> 如果集區大小增加，則值會設定為 [無效]。|
|currentDedicated|Int32|目前指派至集區的計算節點數目。|
|targetDedicated|Int32|向集區要求的計算節點數目。|
|enableAutoScale|Bool|指定集區大小是否隨著時間自動調整。|
|isAutoPool|Bool|指定是否已透過作業的 AutoPool 機制建立集區。|
|startTime|DateTime|集區調整大小開始時間。|
|EndTime|DateTime|集區調整大小完成時間。|
|ResultCode|String|調整大小的結果。|
|resultMessage|String|調整大小錯誤中包含結果的詳細資料。<br /><br /> 如果調整大小已成功完成，表示作業已成功。|