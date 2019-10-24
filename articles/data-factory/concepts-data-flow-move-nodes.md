---
title: Azure Data Factory 資料流程移動節點
description: 如何在 Azure Data Factory 對應的資料流程圖中移動節點
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 631a103491b70ca016b94af01995aeeb3f29c77a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754673"
---
# <a name="mapping-data-flow-move-nodes"></a>對應資料流程移動節點



![匯總轉換選項](media/data-flow/agghead.png "匯總工具標頭")

Azure Data Factory 資料流程設計介面是「建構」介面，其中您要由上至下、由左至右建立資料流程。 每個轉換皆附有工具箱 (加號 (+) 符號)。 專注在商務邏輯，而非透過自由格式 DAG 環境中的邊緣連接節點。

因此，「移動」轉換節點的方式是變更傳入資料流，而不使用拖放架構。 您將改為變更「傳入資料流」來四處移動轉換。

## <a name="streams-of-data-inside-of-data-flow"></a>資料流程內的資料流程

在 Azure Data Factory 資料流程中，資料流代表資料的流程。 在 [轉換設定] 窗格中，您會看到 [傳入資料流程] 欄位。 這會告訴您提供轉換的是哪一個資料流。 您可以按一下傳入資料流名稱，然後選擇另一個資料流，變更圖表上轉換節點的實體位置。 目前的轉換以及該資料流上的所有後續轉換，將會接著移至新位置。

如果您在之後要移動具有一或多個轉換的轉換，則資料流程中的新位置會透過新的分支聯結。

如果您在選擇節點之後沒有後續的轉換，則只有該轉換會移至新的位置。

## <a name="next-steps"></a>後續步驟

完成資料流程設計之後，請開啟 [偵錯工具] 按鈕，然後直接在 [[資料流程設計師](concepts-data-flow-debug-mode.md)] 或 [[管線] debug](control-flow-execute-data-flow-activity.md)中以 [debug] 模式測試它。
