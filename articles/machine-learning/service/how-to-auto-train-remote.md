---
title: 自動化 ML 遠端計算目標
titleSuffix: Azure Machine Learning service
description: 瞭解如何在具有 Azure Machine Learning 服務的 Azure Machine Learning 遠端計算目標上, 使用自動化機器學習來建立模型
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 852190f7b66c0d2c527d1784c72f963e11620064
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371100"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>使用雲端中的自動化機器學習來將模型定型

在 Azure Machine Learning 中，您可以在所管理的不同類型計算資源上將模型定型。 計算目標可以是本機電腦或雲端中的資源。

您可以藉由新增額外的計算目標 (例如 Azure Machine Learning 計算 (AmlCompute), 輕鬆地相應增加或相應放大您的機器學習實驗。 AmlCompute 是受控計算基礎結構, 可讓您輕鬆建立單一或多重節點的計算。

在本文中, 您將瞭解如何使用自動化 ML 搭配 AmlCompute 來建立模型。

## <a name="how-does-remote-differ-from-local"></a>遠端與本機有何不同？

「[使用自動化機器學習來訓練分類模型](tutorial-auto-train-models.md)」教學課程會教您如何使用本機電腦, 透過自動化 ML 來訓練模型。 在本機訓練時的工作流程也適用於遠端目標。 不過，使用遠端計算時，自動化 ML 實驗反覆項目會以非同步方式執行。 此功能可讓您取消特定的反覆項目、監看執行狀態，或繼續處理 Jupyter Notebook 中的其他資料格。 若要從遠端進行定型, 請先建立遠端計算目標, 例如 AmlCompute。 接著，設定遠端資源，並在該處提交您的程式碼。

本文說明在遠端 AmlCompute 目標上執行自動化 ML 實驗所需的額外步驟。 以下程式碼會使用來自教學課程的工作區物件 `ws`。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>建立資源

在您的工作區中建立 AmlCompute`ws`目標 () (如果尚未存在)。

**估計時間**：建立 AmlCompute 目標大約需要5分鐘的時間。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)

compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

您現在可以使用 `compute_target` 物件作為遠端計算目標。

叢集名稱限制包括:
+ 必須少於 64 個字元。
+ 不得包含下列任一字元：`\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-getdata-function"></a>使用 get_data () 函數存取資料

提供訓練資料的遠端資源存取。 針對在遠端計算上執行的自動化機器學習實驗，資料必須使用 `get_data()` 函式加以擷取。

若要提供存取，您必須：
+ 建立包含 `get_data()` 函式的 get_data.py 檔案
+ 將該檔案放在可當成絕對路徑存取的目錄中

您可以在 get_data.py 檔案中封裝用來從 Blob 儲存體或本機磁碟讀取資料的程式碼。 在下列程式碼範例中，資料來自 sklearn 套件。

```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():

    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="create-run-configuration"></a>建立執行設定

若要讓 get_data .py 腳本能夠使用相依性, 請定義`RunConfiguration`已定義`CondaDependencies`的物件。 請在中`AutoMLConfig`使用此`run_configuration`物件做為參數。

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

如需此設計模式的其他範例, 請參閱此[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb)。

## <a name="configure-experiment"></a>設定實驗

指定 `AutoMLConfig` 的設定。  (請參閱[完整參數清單](how-to-configure-auto-train.md#configure-experiment)及其可能值。)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>啟用模型說明

在 `AutoMLConfig` 建構函式中設定選擇性的 `model_explainability` 參數。 此外，驗證資料架構物件必須當做參數 `X_valid` 來傳遞，以使用模型說明能力特徵。

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>提交定型實驗

現在提交設定以自動選取演算法、超參數，並訓練模型。

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

您將會看到類似於下列範例的輸出：

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>瀏覽結果

您可以使用[訓練教學課程](tutorial-auto-train-models.md#explore-the-results)中所示的相同[Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)來查看結果的圖表和資料表。

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

以下是小工具的靜態影像。  在筆記本中，您可以按一下資料表中的任一行以查看執行的屬性，以及該次執行的輸出記錄。   您也可以使用圖表上方的下拉式清單，以針對每個反覆項目檢視每個可用度量的圖表。

![小工具資料表](./media/how-to-auto-train-remote/table.png)
![小工具繪圖](./media/how-to-auto-train-remote/plot.png)

小工具會顯示 URL，您可以使用它來查看並瀏覽個別執行的詳細資料。  

如果您不在 Jupyter 筆記本中, 可以從執行本身顯示 URL:

```
remote_run.get_portal_url()
```

您的工作區中會提供相同的資訊。  若要深入瞭解這些結果, 請參閱[瞭解自動化機器學習結果](how-to-understand-automated-ml.md)。

### <a name="view-logs"></a>檢視記錄檔

在 `/tmp/azureml_run/{iterationid}/azureml-logs` 下方的 DSVM 上尋找記錄。

## <a name="explain"></a>最佳模型說明

擷取模型說明資料可讓您查看有關模型的詳細資訊，以提高要在後端執行之項目的透明度。 在此範例中，您可以僅針對最佳調整模型執行模型說明。 如果您針對管線中的所有模型加以執行，它將會產生大量的執行時間。 模型說明資訊包括：

* shap_values：Shap lib 所產生的說明資訊。
* expected_values：模型的預期值會套用到一組 X_train 資料。
* overall_summary：模型層級的特徵重要性值會以遞減順序排序。
* overall_imp：功能名稱的排序方式與 overall_summary 中的順序相同。
* per_class_summary：類別層級的特徵重要性值會以遞減順序來排序。 僅適用于分類案例。
* per_class_imp：特徵名稱會以與 per_class_summary 相同的順序來排序。 僅適用于分類案例。

使用下列程式碼，從您的反覆項目中選取最佳管線。 `get_output` 方法會針對最後一個調整引動過程，傳回最佳回合和已調整的模型。

```python
best_run, fitted_model = remote_run.get_output()
```

匯入 `retrieve_model_explanation` 函式，並在最佳模型上執行。

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

列印您想要檢視的 `best_run` 說明變數結果。

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

列印 `best_run` 說明摘要變數會產生下列輸出。

![模型說明能力主控台輸出](./media/how-to-auto-train-remote/expl-print.png)

您也可以在工作區內部，透過小工具 UI 以及 Azure 入口網站中的 Web UI，來將特徵重要性視覺化。

![模型說明能力 UI](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>範例

[How-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb)筆記本會示範本文中的概念。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

了解[如何設定自動訓練的設定](how-to-configure-auto-train.md)。
