---
title: 使用自訂的 Docker 基底映射部署模型
titleSuffix: Azure Machine Learning
description: 瞭解如何在部署 Azure Machine Learning 模型時使用自訂的 Docker 基底映射。 部署已定型的模型時，會部署基底容器映射來執行您的模型以進行推斷。 雖然 Azure Machine Learning 會為您提供預設的基底映射，但您也可以使用自己的基底映射。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/22/2019
ms.openlocfilehash: 84567b68c85a48d0fc02f6f6a4986d8092215a92
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326504"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>使用自訂的 Docker 基底映射部署模型

瞭解如何在使用 Azure Machine Learning 部署定型的模型時，使用自訂的 Docker 基底映射。

當您將定型的模型部署至 web 服務或 IoT Edge 裝置時，系統會建立一個套件，其中包含可處理傳入要求的 web 伺服器。

Azure Machine Learning 提供預設的 Docker 基底映射，因此您不必擔心如何建立它。 您也可以使用 Azure Machine Learning__環境__來選取特定的基底映射，或使用您提供的自訂映射。

建立映射以供部署時，會使用基底映射做為起點。 它提供基礎作業系統和元件。 然後部署程式會在部署之前，將其他元件（例如您的模型、conda 環境和其他資產）新增至映射。

一般而言，當您想要使用 Docker 來管理相依性時，您會建立自訂基底映射、更嚴格地控制元件版本，或在部署期間節省時間。 例如，您可能會想要在特定版本的 Python、Conda 或其他元件上進行標準化。 您可能也會想要安裝模型所需的軟體，安裝程式會花很長的時間。 在建立基底映射時安裝軟體，表示您不需要針對每個部署進行安裝。

> [!IMPORTANT]
> 當您部署模型時，無法覆寫核心元件（例如網頁伺服器或 IoT Edge 元件）。 這些元件會提供已知的工作環境，並由 Microsoft 進行測試和支援。

> [!WARNING]
> Microsoft 可能無法協助疑難排解自訂映射所造成的問題。 如果您遇到問題，系統可能會要求您使用預設影像或 Microsoft 提供的其中一個映射，以查看問題是否為您的映射所特有。

本檔分成兩個部分：

* 建立自訂基底映射：提供系統管理員和 DevOps 的資訊，以建立自訂映射，並使用 Azure CLI 和 Machine Learning CLI 設定 Azure Container Registry 的驗證。
* 使用自訂基底映射部署模型：在從 Python SDK 或 ML CLI 部署定型的模型時，為數據科學家和 DevOps/ML 工程師提供使用自訂映射的資訊。

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning workgroup。 如需詳細資訊，請參閱[建立工作區](how-to-manage-workspace.md)文章。
* [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Azure Machine Learning 的 CLI 擴充](reference-azure-machine-learning-cli.md)功能。
* 可在網際網路上存取的[Azure Container Registry](/azure/container-registry)或其他 Docker Registry。
* 本檔中的步驟假設您已熟悉建立和使用__推斷__設定物件做為模型部署的一部分。 如需詳細資訊，請參閱[部署位置和方式](how-to-deploy-and-where.md#prepare-to-deploy)的「準備部署」一節。

## <a name="create-a-custom-base-image"></a>建立自訂基底映射

本節中的資訊假設您使用 Azure Container Registry 來儲存 Docker 映射。 規劃建立 Azure Machine Learning 的自訂映射時，請使用下列檢查清單：

* 您將使用為 Azure Machine Learning 工作區建立的 Azure Container Registry，還是獨立 Azure Container Registry？

    使用儲存在__工作區的容器__登錄中的映射時，您不需要向登錄進行驗證。 驗證是由工作區負責處理。

    > [!WARNING]
    > 第一次使用工作區__訓練或部署模型__時，會建立工作區的 Azure Container Registry。 如果您已建立新的工作區，但未定型或建立模型，則工作區不會有任何 Azure Container Registry。

    如需有關抓取工作區 Azure Container Registry 名稱的詳細資訊，請參閱本文的[取得容器登錄名稱](#getname)一節。

    使用儲存在__獨立容器__登錄中的映射時，您必須設定至少具有「讀取」存取權的服務主體。 接著，您可以將服務主體識別碼（使用者名稱）和密碼提供給使用登錄中映射的任何人。 如果您讓容器登錄可公開存取，就會發生例外狀況。

    如需建立私人 Azure Container Registry 的詳細資訊，請參閱[建立私人容器](/azure/container-registry/container-registry-get-started-azure-cli)登錄。

    如需使用服務主體搭配 Azure Container Registry 的詳細資訊，請參閱[使用服務主體 Azure Container Registry 驗證](/azure/container-registry/container-registry-auth-service-principal)。

* Azure Container Registry 和影像資訊：將映射名稱提供給需要使用它的任何人。 例如，名`myimage`為的映射（儲存在名為`myregistry`的登錄中）會`myregistry.azurecr.io/myimage`作為使用映射進行模型部署時的參考

* 映射需求：Azure Machine Learning 只支援提供下列軟體的 Docker 映射：

    * Ubuntu 16.04 或更新版本。
    * Conda 4.5. # 或更新版本。
    * Python 3.5. # 或 3.6. #。

<a id="getname"></a>

### <a name="get-container-registry-information"></a>取得容器登錄資訊

在本節中，您將瞭解如何取得 Azure Machine Learning 工作區的 Azure Container Registry 名稱。

> [!WARNING]
> 第一次使用工作區__訓練或部署模型__時，會建立工作區的 Azure Container Registry。 如果您已建立新的工作區，但未定型或建立模型，則工作區不會有任何 Azure Container Registry。

如果您已使用 Azure Machine Learning 訓練或部署模型，則會為您的工作區建立容器登錄。 若要尋找此容器登錄的名稱，請使用下列步驟：

1. 開啟新的 shell 或命令提示字元，並使用下列命令來驗證您的 Azure 訂用帳戶：

    ```azurecli-interactive
    az login
    ```

    遵循提示向訂用帳戶進行驗證。

2. 使用下列命令來列出工作區的容器登錄。 將`<myworkspace>`取代為您的 Azure Machine Learning 工作區名稱。 將`<resourcegroup>`取代為包含您工作區的 Azure 資源群組:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

    所傳回的資訊類似下列文字︰

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`值是您工作區的 Azure Container Registry 名稱。

### <a name="build-a-custom-base-image"></a>建立自訂基底映射

本節中的步驟將逐步解說如何在您的 Azure Container Registry 中建立自訂的 Docker 映射。

1. 建立名為`Dockerfile`的新文字檔，並使用下列文字做為內容：

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 從 shell 或命令提示字元，使用下列命令來驗證 Azure Container Registry。 `<registry_name>`以您想要用來儲存映射的容器登錄名稱取代：

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. 若要上傳 Dockerfile，並建立它，請使用下列命令。 以`<registry_name>`您想要用來儲存映射的容器登錄名稱取代：

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    在建立程式期間，會將資訊串流處理回命令列。 如果組建成功，您會收到類似下列文字的訊息：

    ```text
    Run ID: cda was successful after 2m56s
    ```

如需使用 Azure Container Registry 建立映射的詳細資訊，請參閱[使用 Azure Container Registry 工作建立和執行容器映射](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

如需將現有映射上傳至 Azure Container Registry 的詳細資訊，請參閱將[您的第一個映射推送至私人 Docker 容器](/azure/container-registry/container-registry-get-started-docker-cli)登錄。

## <a name="use-a-custom-base-image"></a>使用自訂基底映射

若要使用自訂映射，您需要下列資訊：

* __映射名稱__。 例如， `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`是 Microsoft 所提供的基本 Docker 映射的路徑。
* 如果映射位於__私人存放庫__中，您需要下列資訊：

    * 登錄__位址__。 例如： `myregistry.azureecr.io` 。
    * 具有登錄讀取權限的服務主體使用者__名稱__和__密碼__。

    如果您沒有此資訊，請向系統管理員詢問包含您映射的 Azure Container Registry。

### <a name="publicly-available-base-images"></a>公開可用的基底映射

Microsoft 會在可公開存取的儲存機制上提供數個 docker 映射，此存放庫可以與本節中的步驟搭配使用：

| Image | 描述 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning 的基本映射 |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | 包含 CPU inferecning 的 ONNX 執行時間 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | 包含適用于 GPU 的 ONNX 執行時間和 CUDA |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | 包含適用于 GPU 的 ONNX 執行時間和 TensorRT |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | 包含 ONNX 執行時間和 OpenVINO，<sup> </sup>適用于以 Movidius<sup>TM</sup> MyriadX VPUs 為基礎的 Intel 視覺加速器設計 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | 包含適用于 Intel<sup> </sup> Movidius<sup>TM</sup> USB 記憶杆的 ONNX Runtime 和 OpenVINO |

如需 ONNX 執行時間基底映射的詳細資訊，請參閱 GitHub 存放庫中的[ONNX Runtime dockerfile 一節](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md)。

> [!TIP]
> 由於這些映射可公開使用，因此當您使用時，不需要提供位址、使用者名稱或密碼。

如需詳細資訊，請參閱[Azure Machine Learning 容器](https://github.com/Azure/AzureML-Containers)。

> [!TIP]
>__如果您的模型是在 Azure Machine Learning 計算上定型__，使用__版本1.0.22 或更高__的 Azure Machine Learning SDK，則會在定型期間建立映射。 若要探索此映射的名稱，請`run.properties["AzureML.DerivedImageName"]`使用。 下列範例示範如何使用此映射：
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>使用 Azure Machine Learning SDK 的映射

若要使用儲存在**工作區的 Azure Container Registry**中的映射，或可**公開存取的容器**登錄，請設定下列[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)屬性：

+ `docker.enabled=True`
+ `docker.base_image`:將設定為登錄和映射的路徑。

```python
from azureml.core import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

若要使用不在您工作區中的__私人容器__登錄中的映射，您必須`docker.base_image_registry`使用來指定存放庫的位址，以及使用者名稱和密碼：

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"
```

定義環境之後，請將它與[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)物件搭配使用，以定義將在其中執行模型和 web 服務的推斷環境。

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

此時，您可以繼續進行部署。 例如，下列程式碼片段會使用推斷設定和自訂映射在本機部署 web 服務：

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

如需部署的詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

### <a name="use-an-image-with-the-machine-learning-cli"></a>使用 Machine Learning CLI 的映射

> [!IMPORTANT]
> 目前 Machine Learning CLI 可以使用來自您工作區或可公開存取之儲存機制的 Azure Container Registry 映射。 它無法使用獨立私人登錄中的映射。

使用 Machine Learning CLI 部署模型時，您會提供參考自訂映射的推斷設定檔案。 下列 JSON 檔示範如何在公用容器登錄中參考映射：

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

此檔案會搭配`az ml model deploy`命令使用。 `--ic`參數是用來指定推斷設定檔案。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

如需使用 ML CLI 部署模型的詳細資訊，請參閱[Azure Machine Learning 的 CLI 延伸](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)模組的 < 模型註冊、分析和部署一節。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[部署的位置和](how-to-deploy-and-where.md)做法。
* 瞭解如何[使用 Azure Pipelines 來定型和部署機器學習模型](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)。
