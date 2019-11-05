---
title: 瞭解自動化 ML 結果
titleSuffix: Azure Machine Learning
description: 瞭解如何針對每個自動化機器學習服務執行，查看並瞭解其圖表和計量。
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695e0bbcb81a570519a6f74cfdeab4ef85f076
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489400"
---
# <a name="understand-automated-machine-learning-results"></a>瞭解自動化機器學習結果
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何查看並瞭解每個自動化機器學習服務執行的圖表和計量。 

深入了解：
+ [分類模型的計量、圖表和曲線](#classification)
+ [回歸模型的計量、圖表和圖形](#regression)
+ [模型 interpretability 和功能重要性](#explain-model)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree) 。

* 使用 SDK 或在 Azure Machine Learning studio 中，為您的自動化機器學習執行建立實驗。

    * 使用 SDK 來建立[分類模型](how-to-auto-train-remote.md)或[回歸模型](tutorial-auto-train-models.md)
    * 使用[Azure Machine Learning studio](how-to-create-portal-experiments.md) ，藉由上傳適當的資料來建立分類或回歸模型。

## <a name="view-the-run"></a>查看執行

執行自動化機器學習實驗之後，您可以在機器學習服務工作區中找到回合的歷程記錄。 

1. 移至工作區。

1. 在工作區的左面板中，選取 [**實驗**]。

   ![實驗功能表的螢幕擷取畫面](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 在實驗清單中，選取您想要探索的測試。

   [![實驗清單](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 在底部的資料表中，選取 [**執行編號**]。

   [![實驗執行](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)）

1. 在 [反覆運算] 資料表中，選取您想要進一步探索之模型的**反復專案編號**。

   [![實驗模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

當您使用 `RunDetails`[Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)時，您也會在執行期間看到這些相同的結果。

## <a name="classification"></a>分類結果

您可以使用 Azure Machine Learning 的自動化機器學習功能，針對您建立的每個分類模型提供下列計量和圖表三個

+ [計量](#classification-metrics)
+ [混淆矩陣](#confusion-matrix)
+ [精確度與召回率圖表](#precision-recall-chart)
+ [接收者操作特徵 (或 ROC)](#roc)
+ [升力曲線](#lift-curve)
+ [增益曲線](#gains-curve)
+ [校正圖](#calibration-plot)

### <a name="classification-metrics"></a>分類計量

針對分類工作，會在每次執行反復專案中儲存下列計量。

|計量|說明|計算|額外的參數
--|--|--|--|
AUC_Macro| AUC 是「接收者作業特性曲線」下方的面積。 Macro 是每個類別 AUC 的算術平均值。  | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC 是「接收者作業特性曲線」下方的面積。 微的計算方式是將每個類別的真肯定和假陽性結合在一起。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC 是「接收者作業特性曲線」下方的面積。 加權是每個類別的分數算術平均值，以每個類別中 true 實例的數目加權。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
精確度|精確度是完全符合 true 標籤的預測標籤百分比。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 Macro 是每個類別之平均精確度分數的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 微的計算方式是在每個截止時結合真肯定和誤報。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 加權是每個類別平均精確度分數的算術平均值，以每個類別中 true 實例的數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy 是每個類別其召回率的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 分數是精確度和召回率的調和平均數。 Macro 是每個類別的 F1 分數算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 分數是精確度和召回率的調和平均數。 微運算是透過計算真肯定、誤否定和誤報的總計來計算。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 分數是精確度和召回率的調和平均數。 以每個類別的 F1 分數其類別頻率將平均值加權|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|這是損失函數，用於 (多維度) 羅吉斯迴歸與其擴充功能中，例如類神經網路，如果有機率分類器的預測時，則定義為 true 標籤的負數對數似然比。 針對 {0,1} 中具有 true 標籤 yt 的單一範例，以及 yt = 1 的估計機率 yp，記錄遺失為-log P （&#124;yt yp） =-（yt log （yp） + （1-yt） log （1-yp））。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalized Macro Recall 是正常化的 Macro Recall，因此隨機效能的分數為 0，完美效能的分數為 1。 這是藉由 norm_macro_recall： = （recall_score_macro-R）/（1-R）來達成，其中 R 是隨機預測的預期 recall_score_macro 值（亦即，二元分類的 R = 0.5 和 C 類別分類問題的 R = （1/C））。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "宏" |
precision_score_macro|Precision 是標示為真正在該類別中特定類別的元素百分比。 Macro 是每個類別之精確度的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision 是標示為真正在該類別中特定類別的元素百分比。 微運算會藉由計算真肯定和誤報的總計來計算全域。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision 是標示為真正在該類別中特定類別的元素百分比。 加權是每個類別的精確度算術平均值，並依每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall 是真正在已正確標示的特定類別中的元素百分比。 Macro 是每個類別的召回算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall 是真正在已正確標示的特定類別中的元素百分比。 微運算會藉由計算真肯定、誤否定和誤報的總計來計算全域|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall 是真正在已正確標示的特定類別中的元素百分比。 加權是每個類別的召回算術平均值，並依每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|加權精確度是精確度，其中每個範例所提供的權數等於該範例的 true 類別中 true 實例的比例。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是一種向量，等於目標中每個元素為該類別的比例|

### <a name="confusion-matrix"></a>混淆矩陣

混淆矩陣可用來說明分類模型的效能。 每一列都會顯示真值類別的執行個體，而每一行均代表預測類別的執行個體。 混淆矩陣會針對指定的模型來顯示分類正確的標籤和分類不正確的標籤。

針對分類問題，Azure Machine Learning 會針對每個已建置的模型自動提供混淆矩陣。 針對每個混淆矩陣，自動化 ML 會顯示每個預測標籤和每個實際標籤交集的頻率。 顏色愈深，表示矩陣的特定部分中的計數愈高。 在理想的情況下，最暗的色彩會沿著矩陣的對角線。 

範例1：精確度不佳的分類模型 ![精確度不佳的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

範例2：具有高準確度的分類模型（理想） ![具有高準確度的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>精確度與召回率圖表

利用此圖表，您可以比較每個模型的精確度與召回率曲線，以針對您的特定商務問題來判斷哪一個模型在精確度與召回率之間具有可接受的關聯性。 此圖表顯示宏平均精確度與召回率、微平均精確度與召回率，以及與模型之所有類別相關聯的精確度與召回率。

精確度一詞代表分類器能夠正確標示所有執行個體。 召回率代表分類器能夠針對特定標籤找到的所有執行個體。 精確度與召回率曲線會顯示這兩個概念之間的關聯性。 在理想情況下，此模型會有 100% 的精確度和 100% 的準確度。

範例1：具有低精確度與低召回率的分類模型 ![具有低精確度和低召回的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

範例2：分類模型的精確度為 ~ 100%，而 ~ 100% 召回（理想） ![分類模型的高精確度和召回](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

接收者操作特徵 (或 ROC) 是對於特定模型之分類正確標籤與分類不正確標籤的繪圖。 在具有高偏差的資料集上將模型定型時，ROC 曲線可提供的資訊較少，因為它將不會顯示誤判標籤。

範例1：具有低 true 標籤和高 false 標籤的分類模型 ![具有低 true 標籤和高 false 標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

範例2：具有高 true 標籤和低 false 標籤的分類模型 ![具有高 true 標籤和低 false 標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>升力曲線

您可以將使用 Azure Machine Learning 自動建置的模型升力與基準進行比較，以檢視該特定模型的值增益。

升力圖可用來評估分類模型的效能。 它會顯示相較於不使用模型，您能夠預期使用模型會做得更好。 

範例1：模型的執行比隨機選取模型更糟 ![比隨機選取模型更糟的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

範例2：模型的執行效果優於隨機選取模型 ![執行效能更佳的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>增益曲線

增益圖會依每個部分的資料來評估分類模型的效能。 它會顯示資料集的每個百分位數值，相較於隨機選取模型，您能夠預期它會執行得更好。

使用累計增益圖，可協助您使用對應至模型中所需增益的百分比來選擇分類截止。 此資訊提供另一種方式來查看隨附升力圖中的結果。

範例1：具有最低增益的分類模型 ![具有最少增益的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

範例2：具有大幅增益的分類模型 ![具有顯著增益的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>校正圖

針對所有分類問題，您可以檢閱微平均、宏平均及指定預測模型中每個類別的校正線。 

校正圖可用來顯示預測模型的信賴度。 它會顯示預測機率與實際機率之間的關聯性，其中「機率」代表特定執行個體屬於某個標籤的可能性。 經過準確校正的模型會與 y=x 線對齊，在其預測中具有合理的信賴度。 過度信賴的模型會與 y=0 線對齊，其會顯示預測機率，但沒有任何實際機率。

範例1：更妥善校正的模型 ![ 更妥善校正的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

範例2：過度信賴的模型 ![過度信賴的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>回歸結果

三個下列計量和圖表適用于您使用的自動化機器學習功能所建立的每個回歸模型 Azure Machine Learning

+ [計量](#reg-metrics)
+ [預測與 True](#pvt)
+ [殘差直方圖](#histo)


### <a name="reg-metrics"></a>回歸計量

下列計量會儲存在回歸或預測工作的每次執行反復專案中。

|計量|說明|計算|額外的參數
--|--|--|--|
explained_variance|Explained variance 是所給予資料集其變化的數學模型帳戶的比例。 它是原始資料其變異數中減少至錯誤變異數的百分比。 當錯誤的平均值為 0 時，它會等於 Explained variance。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 是與輸出平均值的基線模型相比的確定係數，或平方誤差減少的百分比。 |[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Spearman correlation (斯皮爾曼相關性) 是兩個資料集之間關係其單調性的非參數量值。 不同於 Pearson correlation (皮耳森相關性)，Spearman correlation 不假設這兩個資料集為常態分佈。 如同其他的相關係數，此相關係數的變化在 -1 到 +1 之間，其中 0 代表不相關。 -1 或 + 1 的相互關聯表示真正單純的關聯性。 正相關是指隨著 x 增加，y 也會增加。 負相關是指隨著 x 增加，y 會減少。|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Mean absolute error (平均絕對誤差) 是目標與預測值之間差異絕對值的預期值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalized mean absolute error (正規化平均絕對誤差) 是平均絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以資料範圍|
median_absolute_error|Median absolute error (中位數絕對誤差) 是目標與預測值之間所有絕對值差異的中位數。 此遺失是強固極端值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalized median absolute error (正規化中位數絕對誤差) 是中位數絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以資料範圍|
root_mean_squared_error|Root mean squared error (均方根誤差) 是目標與預測值之間預期平方差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalized root mean squared error (正規化均方根誤差) 是均方根誤差防以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以資料範圍|
root_mean_squared_log_error|Root mean squared log error (均方根對數誤差) 是預期平方對數誤差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error (正規化均方根對數誤差) 是均方根對數誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以資料範圍|

### <a name="pvt"></a>預測與 True

[已預測] 與 [True] 會顯示預測值與回歸問題的相關 True 值之間的關聯性。 此圖表可用來測量模型的效能，因為預設值愈接近 y=x 線，預測模型的準確度就愈好。

在每個回合之後，您都能查看每個迴歸模型的預測與真值圖。 為了保護資料隱私權，會將值組合在一起，而每組的大小均會顯示為圖表區域下半部的長條圖。 您可以根據模型所在的理想值，將預測模型與顯示誤差幅度且顏色較淡的陰影區域進行比較。

範例1：預測中低精確度的回歸模型 ![預測中的低精確度的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

範例2：預測中具有高精確度的回歸模型[![其預測中具有高精確度的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>殘差的長條圖

殘差代表觀察到的 y - 預測的 y。 若要顯示低偏差的錯誤幅度，殘差直方圖應該會形成以 0 為中心的鐘形曲線。 

範例1：在其錯誤中有偏差的回歸模型 ![在其錯誤中有偏差的 SA 回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

範例2：更平均分配錯誤的回歸模型 ![回歸模型，更平均地分配錯誤](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>模型 interpretability 和功能重要性

功能重要性可讓您查看每項功能在模型的結構中有多重要。 預設會關閉此計算，因為它可能會大幅增加執行時間。   您可以啟用所有模型的模型說明，或僅說明最適合的模型。

您可以檢閱整個模型以及預測模型上每個類別的特徵重要性分數。 您可以查看每個特徵的重要性如何針對每個類別及整體進行比較。

![特徵說明能力](./media/how-to-understand-automated-ml/feature-importance.gif)

如需有關啟用 interpretability 功能的詳細資訊，請參閱[如何](how-to-machine-learning-interpretability-automl.md)啟用自動化 ML 實驗中的 interpretability。

## <a name="next-steps"></a>後續步驟

+ 深入瞭解 Azure Machine Learning 中的[自動化 ml](concept-automated-ml.md) 。
+ 試用[自動化的 Machine Learning 模型說明](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)範例筆記本。
