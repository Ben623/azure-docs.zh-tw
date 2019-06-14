---
title: Azure Data Factory 對應資料流程 [檢查] 和 [資料預覽]
description: Azure Data Factory 對應資料流程具有可顯示資料流程中繼資料的窗格 ([檢查])，以及在偵錯模式中可顯示資料預覽的窗格 ([資料預覽])
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 47cde50e0874f0f73523925b6d1b2f8ee4abaea9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61284018"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Azure Data Factory 對應資料流程轉換 [檢查] 索引標籤

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![[檢查] 窗格](media/data-flow/agg3.png "[檢查] 窗格")

[檢查] 窗格能讓您檢視正在轉換之資料流的中繼資料。 您將能看見資料行計數、變更的資料行、新增的資料行、資料類型、資料行排序，以及資料行參考。 [檢查] 是您中繼資料的唯讀檢視。 您不需要啟用偵錯模式便可以在 [檢查] 窗格中查看中繼資料。

在您透過轉換變更資料的形狀時，您將會看到中繼資料變更流經 [檢查] 窗格。 如果您的來源轉換中沒有已定義的結構描述，中繼資料將不會在 [檢查] 窗格中顯示。 結構描述漂移案例經常會發生缺乏中繼資料的情況。

![資料預覽](media/data-flow/datapreview.png "資料預覽")

[資料預覽] 窗格能提供正在轉換之資料的唯讀檢視。 您可以檢視轉換和運算式的輸出，來驗證資料流程。 您必須開啟偵錯模式才能查看資料預覽。 當您按一下偵錯預覽方格中的資料行時，系統隨即會在右側顯示面板。 該快顯面板將會顯示您所選取之每個資料行的設定檔統計資料。

![資料預覽圖表](media/data-flow/chart.png "資料預覽圖表")

