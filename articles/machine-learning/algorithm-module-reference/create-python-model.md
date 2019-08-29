---
title: 建立 Python 模型:模組參考
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務中的建立 Python 模型模型來建立自訂模型或資料處理模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: c6d7aabd41e9d0e872926adbbcb2d18332cb7d5e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128929"
---
# <a name="create-python-model"></a>建立 Python 模型

本文說明如何使用 [**建立 Python 模型**] 模組, 從 Python 腳本建立未定型的模型。 

您可以根據 Azure Machine Learning 環境中 Python 套件所包含的任何學習模組來建立模型的基礎。 

建立模型之後, 您可以使用 [[定型模型](train-model.md)] 將資料集上的模型定型, 如同 Azure Machine Learning 中的任何其他學習模組。 定型的模型可以傳遞至[評分模型](score-model.md), 以使用模型進行預測。 然後可以儲存已定型的模型, 並將評分工作流程發佈為 web 服務。

> [!WARNING]
> 目前無法傳遞 Python 模型的評分結果來[評估模型](evaluate-model.md)。 如果您需要評估模型, 您可以撰寫自訂 Python 腳本, 並使用[執行 Python 腳本](execute-python-script.md)模組來執行它。  


## <a name="how-to-configure-create-python-model"></a>如何設定建立 Python 模型

使用此模組需要 Python 的中繼或專家知識。 模組支援使用已安裝在 Azure Machine Learning 中的 Python 套件所包含的任何學習模組。 請參閱[執行 Python 腳本](execute-python-script.md)中預先安裝的 python 套件清單。
  

本文將說明如何搭配簡單的實驗來使用**建立 Python 模型**。 以下是實驗的圖表。

![create-python-model](./media/module/aml-create-python-model.png)

1.  按一下 [**建立 Python 模型**], 編輯腳本來執行您的模型化或資料管理處理常式。 您可以根據 Azure Machine Learning 環境中 Python 套件所包含的任何學習模組來建立模型的基礎。


    以下是使用熱門*sklearn*封裝的兩個類別貝氏貝氏機率分類分類器的範例程式碼。

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. 將您剛建立的「**建立 Python 模型**」模組連接到**訓練模型**和**評分模型**

3. 如果您需要評估模型, 請新增[執行 Python 腳本](execute-python-script.md), 並編輯 Python 腳本來執行評估。

以下是範例評估程式碼。

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
