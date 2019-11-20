---
title: 設計工具：預測信用風險範例
titleSuffix: Azure Machine Learning
description: 建立分類器並使用自訂 Python 腳本，利用 Azure Machine Learning 設計工具來預測信用風險。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 0bf69683fc5afe24e0e7977b05892c3c10b0cd46
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196095"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>建立分類器 & 使用 Python 腳本來預測使用 Azure Machine Learning 設計工具的信用風險

**設計工具（預覽）範例4**

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

本文說明如何使用設計工具（預覽）來建立複雜的機器學習管線。 您將瞭解如何使用 Python 腳本來執行自訂邏輯，並比較多個模型來選擇最佳選項。

這個範例會將分類器定型，以使用信用額度記錄、年齡和信用卡號碼等點數應用程式資訊來預測信用風險。 不過，您可以套用本文中的概念來處理您自己的機器學習問題。

以下是此管線的完成圖形：

[管線![圖形](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>先決條件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 4] 將它開啟。

## <a name="data"></a>資料

此範例會使用來自 UC Irvine 存放庫的德國信用卡資料集。 其中包含具有20個功能和一個標籤的1000範例。 每個範例都代表一個人。 20個功能包含數值和類別功能。 如需有關資料集的詳細資訊，請參閱[UCI 網站](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)。 最後一個資料行是代表信用風險的標籤，只有兩個可能的值： [高信用風險 = 2] 和 [低信用風險] = 1。

## <a name="pipeline-summary"></a>管線摘要

在此管線中，您比較兩種不同的方法來產生模型，以解決此問題：

- 使用原始資料集進行定型。
- 使用複寫的資料集進行定型。

使用這兩種方法時，您會使用測試資料集搭配複寫來評估模型，以確保結果與成本函數一致。 使用這兩種方法測試兩個分類器：**雙類別支援向量機器**和**雙類別促進式決策樹**。

將低風險範例比為 [高] 的成本是1，而將高風險範例比為 [低] 的成本則是5。 我們會使用**執行 Python 腳本**模組來考慮此分類誤判成本。

以下是管線的圖形：

[管線![圖形](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>資料處理

首先，使用 [**中繼資料編輯器**] 模組來加入資料行名稱，以更有意義的名稱取代預設的資料行名稱，並從 UCI 網站上的資料集描述取得。 在 [**中繼資料編輯器**] 的 [**新資料行**名稱] 欄位中，提供新的資料行名稱做為逗號分隔值。

接下來，產生用來開發風險預測模型的定型和測試集。 使用**分割資料**模組，將原始資料集分割成相同大小的定型和測試集。 若要建立相等大小的集合，請將**第一個輸出資料集選項中的資料列分數**設定為0.7。

### <a name="generate-the-new-dataset"></a>產生新的資料集

由於低估風險的成本很高，因此請設定分類誤判的成本，如下所示：

- 針對高風險的案例，分類錯誤為低風險：5
- 針對低風險的案例，分類錯誤為高風險：1

若要反映此成本函數，請產生新的資料集。 在新的資料集中，每個高風險範例都會複寫五次，但低風險範例的數目不會變更。 在複寫之前將資料分割成定型和測試資料集，以避免在這兩個集合中出現相同的資料列。

若要複寫高風險的資料，請將此 Python 程式碼放入**執行 Python 腳本**模組：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

[**執行 Python 腳本**] 模組會複寫定型和測試資料集。

### <a name="feature-engineering"></a>特徵設計

**雙類別支援向量機器**演算法需要正規化資料。 因此，請使用正規化**資料**模組，將所有數值特徵的範圍標準化，並 `tanh` 轉換。 `tanh` 轉換會將所有數值特徵轉換成0和1範圍內的值，同時保留值的整體分佈。

二元**支援向量機器**模組會處理字串功能，將它們轉換成類別特徵，然後再轉換為值為零或1的二進位特徵。 因此，您不需要將這些功能標準化。

## <a name="models"></a>模型

因為您套用了兩個分類器、**兩個類別的支援向量機器**（SVM）和兩個類別的推進式**決策樹**，以及兩個資料集，所以總共會產生四個模型：

- 以原始資料定型的 SVM。
- SVM 已使用複寫的資料進行定型。
- 以原始資料定型的推進式決策樹。
- 以複寫的資料定型的推進式決策樹。

這個範例會使用標準資料科學工作流程來建立、定型和測試模型：

1. 使用**二級支援向量機器**和**二級促進式決策樹**來初始化學習演算法。
1. 使用**訓練模型**將演算法套用至資料，並建立實際的模型。
1. 使用**評分模型**，透過測試範例來產生分數。

下圖顯示此管線的一部分，其中會使用原始和已複寫的定型集來定型兩個不同的 SVM 模型。 **定型模型**會連接到定型集，而**評分模型**會連接到測試集。

![管線圖表](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

在管線的評估階段中，您會計算四個模型的精確度。 針對此管線，使用 [**評估模型**] 來比較具有相同分類誤判成本的範例。

[**評估模型**] 模組可以計算多達兩個評分模型的效能計量。 因此，您可以使用一個**評估模型**的實例來評估兩個 SVM 模型和另一個 [**評估模型**] 實例，以評估兩個推進式決策樹模型。

請注意，複寫的測試資料集會當做**評分模型**的輸入使用。 換句話說，最終的精確度分數會包含取得標籤錯誤的成本。

## <a name="combine-multiple-results"></a>合併多個結果

「**評估模型**」模組會產生一個包含各種計量的單一資料列的資料表。 若要建立一組精確度結果，我們會先使用 [**加入資料列**] 將結果合併成單一資料表。 然後，我們會在 [**執行 Python 腳本**] 模組中使用下列 Python 腳本，針對結果資料表中的每個資料列加入模型名稱和定型方法：

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>結果

若要查看管線的結果，您可以用滑鼠右鍵按一下 [**資料集中最後一個選取資料行**] 模組的 [視覺化輸出]。

![將輸出視覺化](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

第一個資料行列出用來產生模型的機器學習演算法。

第二個數據行表示定型集的類型。

第三個數據行包含成本相關的精確度值。

從這些結果中，您可以看到，以**雙類別支援向量機器**建立的模型所提供的最佳準確度，並已在複寫的訓練資料集上定型。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索適用于設計工具的其他範例：

- [範例 1-回歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3-使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 5-分類：預測流失](how-to-designer-sample-classification-churn.md)
- [範例 6-分類：預測航班延誤](how-to-designer-sample-classification-flight-delay.md)
- [範例 7-文字分類：維琪百科 SP 500 資料集](how-to-designer-sample-text-classification.md)
