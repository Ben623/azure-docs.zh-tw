---
title: Azure Data Factory 對應資料流程偵錯模式
description: 在建置資料流程時啟動互動式偵錯工作階段
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: a50778db5fd57202c17f05407045259371912586
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239200"
---
# <a name="mapping-data-flow-debug-mode"></a>對應資料流程偵錯模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory 對應資料 Flow 有偵錯模式中，可以在設計介面頂端的 [資料流程偵錯] 按鈕以開啟。 在設計資料流程時，設定開啟偵錯模式可讓您在建置和偵錯資料流程時，以互動方式觀看資料圖形轉換。 在 資料流程設計研討會，以及資料流程管線偵錯執行期間，可以使用偵錯工作階段。

![偵錯 按鈕](media/data-flow/debugbutton.png "偵錯 按鈕")

## <a name="overview"></a>概觀
開啟偵錯模式時，您會以互動方式建立您的使用中的 Spark 叢集的資料流程。 在 Azure Data Factory 中關閉偵錯後，將會關閉此工作階段。 您應該留意在您已開啟偵錯工作階段的期間內，由 Azure Databricks 所產生的每小時費用。

在大部分情況下，最好建置在偵錯模式中您的資料流程，以便在 Azure Data Factory 中發佈您的工作之前，先驗證您的商務邏輯和檢視您的資料轉換。 您也應該使用 [管線] 面板上的 [偵錯] 按鈕來測試您在管線內的資料流程。

> [!NOTE]
> 在 Data Factory 工具列上的綠色偵錯模式燈時，您將資料流程偵錯費率收費 8 核心/小時的一般計算的 60 分鐘與存留時間 

## <a name="debug-mode-on"></a>在 偵錯模式
當您開始偵錯模式時，系統會以側邊面板表單提示您，要求您指向您的互動式 Azure Databricks 叢集並選取來源取樣的選項。 您必須從 Azure Databricks 使用互動式叢集，並從每個來源轉換選取取樣大小，或挑選要用於測試資料的文字檔。

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>在資料流程的偵錯模式中執行時，您的資料不會寫入至接收轉換。 偵錯工作階段預定作為轉換的測試載入器。 偵錯期間不需要接收，而且會在資料流程中忽略。 如果您想要在接收中測試寫入資料，請從 Azure Data Factory 管線執行資料流程並從管線使用偵錯執行。

## <a name="debug-settings"></a>偵錯設定
偵錯設定可從您的資料流的每個來源會出現在側邊面板，然後選取 資料流程 設計工具工具列上的 來源設定 」，也可以編輯。 您可以選取限制及/或檔案來源，以便在此用於您的每個來源轉換。 此設定中的資料列限制為僅針對目前的偵錯工作階段。 您也可以使用在來源中的 [取樣] 設定來限制來源轉換成的資料列。

## <a name="cluster-status"></a>叢集狀態
設計介面的頂端有叢集狀態指示器，它會在叢集準備好進行偵錯時變成綠色。 如果您的叢集已經暖機，則綠色指示器幾乎會立即出現。 如果您的叢集在您進入偵錯模式時尚未執行，，則必須等候 5-7 分鐘才能啟動叢集。 在準備就緒之前，指示器燈號會是黃色。 您的叢集準備好進行資料流程偵錯後，指示器燈號就會變成綠色。

當您完成偵錯，關閉偵錯參數，讓您的 Azure Databricks 叢集可以終止，並在您將不再以偵錯活動。

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>資料預覽
若開啟偵錯，[資料預覽] 索引標籤在底部面板上亮起。 若未開啟偵錯模式，資料流程會顯示只有目前中繼資料進出 [檢查] 索引標籤中的每個轉換。資料預覽只會查詢您已設定為您的限制，在偵錯設定中的資料列數目。 您可能需要按一下 [擷取資料] 來重新整理資料預覽。

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>資料設定檔
選取資料預覽索引標籤中的個別資料行，會在您資料方格的最右邊快顯圖表，其中包含每個欄位的詳細統計資料。 Azure Data Factory 會根據要顯示圖表類型的資料取樣近進行判斷。 高基數欄位會預設為 NULL / NOT NULL 圖表，而具有低基數的類別和數值資料會顯示橫條圖，以顯示資料值的頻率。 您也會看到字串欄位的最大 / len 長度、數值欄位中的最小 / 最大值、標準差、百分位數、計數和平均值。 

<img src="media/data-flow/chart.png" width="400">

## <a name="next-steps"></a>後續步驟

當您完成的建置和偵錯您的資料流程，[從管線中執行它。](control-flow-execute-data-flow-activity.md)

測試時的資料流程管線，使用管線[偵錯執行 [執行] 選項。](iterative-development-debugging.md)
