---
title: 新增資料行：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [新增資料行] 模組來串連兩個資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 55981279cb1902424d1a0f77af097dc379d7222f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493976"
---
# <a name="add-columns-module"></a>新增資料行模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來串連兩個資料集。 您可以將指定為輸入的兩個資料集的所有資料行結合，以建立單一資料集。 如果您需要串連兩個以上的資料集，請使用多個**新增資料行**的實例。



## <a name="how-to-configure-add-columns"></a>如何設定新增資料行
1. 將 [**新增資料行**] 模組新增至您的管線。

2. 連接您想要串連的兩個資料集。 如果您想要合併兩個以上的資料集，您可以結合數個**加入欄**的組合。

    - 您可以結合兩個具有不同資料列數目的資料行。 輸出資料集會以較小來源資料行中每一個資料列的遺漏值填補。

    - 您無法選擇要新增的個別資料行。 當您使用 [**加入資料行**] 時，會串連每個資料集的所有資料行。 因此，如果您只想要加入資料行的子集，請使用 [選取資料集中的資料行]，以您想要的資料行來建立資料集。

3. 執行管道。

### <a name="results"></a>結果
執行管線之後：

- 若要查看新資料集的前幾個資料列，請以滑鼠右鍵按一下 [**加入**資料行] 的輸出，然後選取 [視覺化]。

新資料集內的資料行數目等於兩個輸入資料集的資料行總和。

如果輸入資料集內有兩個數據行具有相同的名稱，則會將數值尾碼加入至資料行的名稱。 例如，如果有兩個名為 TargetOutcome 的資料行實例，則會將左資料行重新命名 TargetOutcome_1 而且會將右邊的資料行重新命名為 TargetOutcome_2。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 