---
title: 機器學習 CLI 擴充功能
titleSuffix: Azure Machine Learning service
description: 了解 Azure CLI 的 Azure Machine Learning CLI 擴充功能。 Azure CLI 是一個跨平台命令列公用工具，可讓您使用 Azure 雲端的資源。 Machine Learning 擴充功能可讓您使用 Azure Machine Learning 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 541ffe70ae5198e631568584a58d02ac283e89d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298246"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>使用 Azure Machine Learning 服務的 CLI 擴充功能

Azure Machine Learning CLI 是 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 的擴充功能，而 Azure CLI 是 Azure 平台的跨平台命令列介面。 此延伸模組會提供使用 Azure Machine Learning 服務的命令。 它可讓您自動化您的機器學習活動。 下列清單提供您可以使用 CLI 擴充功能執行一些範例動作：

+ 執行實驗來建立機器學習服務模型

+ 註冊供客戶使用的機器學習服務模型

+ 封裝、部署和追蹤機器學習模型的生命週期

CLI 不是 Azure Machine Learning SDK 的取代項目。 這是已最佳化，可處理高參數化的工作自動化以符合本身的互補工具。

## <a name="prerequisites"></a>必要條件

* 若要使用 CLI，您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="full-reference-docs"></a>完整的參考文件

尋找[完整的 Azure CLI 的 azure-cli-ml 延伸模組的參考文件](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)。

## <a name="install-the-extension"></a>安裝擴充功能

若要安裝 Machine Learning CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 可以找到範例檔案，您可以使用下列命令搭配[此處](https://aka.ms/azml-deploy-cloud)。

出現提示時，請選取 `y` 來安裝擴充功能。

若要確認已安裝擴充功能，請使用下列命令來顯示 ML 特有的子命令清單：

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>移除擴充功能

若要移除 CLI 擴充功能，請使用下列命令：

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>資源管理

下列命令示範如何使用 CLI 來管理 Azure Machine Learning 所使用的資源。

+ 如果您還沒有一個建立的資源群組：

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ 建立 Azure Machine Learning 服務工作區：

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    如需詳細資訊，請參閱 < [az ml 工作區建立](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)。

+ 附加到資料夾，以啟用 CLI 內容感知的工作區設定。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    此命令會建立`.azureml`子目錄，其中包含範例 runconfig 和 conda 環境檔案。 它也包含`config.json`用來與您的 Azure Machine Learning 工作區通訊的檔案。

    如需詳細資訊，請參閱 < [az ml 資料夾附加](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)。

+ 將 Azure blob 容器中附加的資料存放區。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    如需詳細資訊，請參閱 < [az ml 資料存放區附加 blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)。

    
+ 附加的 AKS 叢集作為計算目標。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    如需詳細資訊，請參閱[az ml computetarget 附加 aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ 建立新的 AMLcompute 目標。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    如需詳細資訊，請參閱 < [az ml computetarget 建立 amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)。

## <a id="experiments"></a>執行實驗

* 開始您的實驗回合。 使用此命令時，指定的 runconfig 檔案名稱 (在之前的文字\*.runconfig，如果您正在查看您的檔案系統) 對-c 參數。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`命令會建立`.azureml`子目錄，其中包含兩個範例 runconfig 檔案。 
    >
    > 如果您有以程式設計方式建立的回合的組態物件的 Python 指令碼時，您可以使用[RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)將其儲存為 runconfig 檔案。
    >
    > 如以上範例的 runconfig 檔案，請參閱 < [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)。

    如需詳細資訊，請參閱 < [az ml 執行提交指令碼](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)。

* 檢視實驗的清單：

    ```azurecli-interactive
    az ml experiment list
    ```

    如需詳細資訊，請參閱 < [az ml 實驗清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)。

## <a name="model-registration-profiling-deployment"></a>模型註冊、 程式碼剖析，部署

下列命令示範如何註冊定型的模型，然後將它部署為生產服務：

+ 向 Azure Machine Learning 註冊模型：

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    如需詳細資訊，請參閱 < [az ml 模型註冊](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)。

+ **選擇性**分析您的模型，以取得最佳的 CPU 和記憶體值進行部署。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    如需詳細資訊，請參閱 < [az ml 模型的設定檔](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

+ 將模型部署到 AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    以下是範例`inferenceconfig.json`文件：
    ```json
    {
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
    }
    ```
    'Deploymentconfig.json' 文件的範例如下：
    ```json
    {
    "computeType": "aks",
    "ComputeTarget": "akscomputetarget"
    }
    ```

    如需詳細資訊，請參閱 < [az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)。


## <a name="next-steps"></a>後續步驟

* [命令的 Machine Learning CLI 擴充功能參考](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)。

* [定型和部署機器學習模型，使用 Azure 的管線](/azure/devops/pipelines/targets/azure-machine-learning)