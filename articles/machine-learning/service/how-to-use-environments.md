---
title: 建立可重複使用的 ML 環境
titleSuffix: Azure Machine Learning
description: 建立和管理用於模型定型和部署的環境。 管理環境的 Python 套件和其他設定。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: b9b58c9b5f32d6ca714ac3ac940b91643fa8020c
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123560"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>重複使用環境，以 Azure Machine Learning 訓練 & 部署。
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何建立和管理 Azure Machine Learning[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，讓您可以追蹤和重現專案的軟體相依性。

軟體相依性管理是適用于開發人員的一般工作。 您想要能夠確保組建可以重現，而不需要進行大量的手動軟體設定。 針對本機開發的解決方案（例如 pip 和 Conda），Azure Machine Learning 環境類別提供本機和分散式雲端開發的解決方案。

本文中的範例示範如何：

* 建立環境並指定套件相依性
* 取出和更新環境
* 使用環境進行訓練
* 使用 web 服務部署的環境

## <a name="what-are-environments"></a>什麼是環境

環境會針對您的訓練和評分腳本，以及執行時間（Python、Spark 或 Docker），指定 Python 套件、環境變數和軟體設定。 它們是您 Azure Machine Learning 工作區內的受控和版本設定實體，可讓您跨不同的計算目標來進行可重現、可審核和便攜的機器學習工作流程。

您可以在本機計算上使用環境物件來開發您的定型腳本、在 Azure Machine Learning 計算上重複使用相同的環境，以進行大規模的模型定型，甚至使用相同的環境部署您的模型。

下圖說明相同的環境物件可用於您的回合設定，以進行定型，以及在您的推斷和部署設定中用於 web 服務部署。

![機器學習服務工作流程中的環境圖表](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>環境的類型

環境大致上可以分成三個類別：**策劃**、**使用者管理**和**系統管理**。

策劃環境是由 Azure Machine Learning 提供，而且預設會在您的工作區中提供。 其中包含 Python 套件和設定的集合，可協助您開始使用不同的機器學習架構。 

針對使用者管理的環境，您需負責設定環境，並在計算目標上安裝定型指令碼所需的每個套件。 Conda 不會檢查您的環境，或為您安裝任何項目。 

當您想要讓[Conda](https://conda.io/docs/)為您管理 Python 環境和腳本相依性時，會使用系統管理的環境。 服務預設會假設這種類型的環境，因為它在不是手動設定的遠端計算目標上有其效用。

## <a name="prerequisites"></a>先決條件

* [已安裝](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)適用于 Python 的 Azure Machine Learning SDK。
* [Azure Machine Learning 工作區](how-to-manage-workspace.md)。


## <a name="create-an-environment"></a>建立環境

有多種方式可為您的實驗建立環境。

### <a name="use-curated-environment"></a>使用策劃環境

您可以選取其中一個要開始的策劃環境。 

* __AzureML-最小__環境包含一組最少的封裝，可啟用執行追蹤和資產上傳。 您可以使用它作為您自己環境的起點。

* __AzureML 教學__課程環境包含常見的資料科學套件，例如 Scikit-learn-學習、Pandas 和 Matplotlib，以及更多的 AzureML sdk 套件集合。

策劃環境是由快取的 Docker 映射所支援，可降低執行準備成本。

使用__環境。 get__方法可選取其中一個策劃環境：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

您可以使用下列程式碼來列出策劃環境及其封裝：
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  請勿以_AzureML_首碼啟動您自己的環境名稱。 它是保留給策劃環境。

### <a name="instantiate-an-environment-object"></a>具現化環境物件

若要手動建立環境，請從 SDK 匯入環境類別，並使用下列程式碼具現化環境物件。

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Conda 和 pip 規格檔案

您也可以從 Conda 規格或 pip 需求檔案建立環境。
使用[from_conda_specification （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-)或[from_pip_requirements （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-)方法，並將您的環境名稱和所需檔案的檔案路徑包含在方法引數中。

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>現有的 Conda 環境

如果您的本機電腦上有現有的 Conda 環境，服務會提供一個解決方案，讓您從中建立環境物件。 如此一來，您就可以在遠端執行上重複使用您的本機互動式環境。

下列程式碼會使用[from_existing_conda_environment （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)方法，從現有的 Conda 環境中建立環境物件 `mycondaenv`。

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>自動建立環境

透過 submit （）方法提交定型回合，自動建立環境。 當您提交定型回合時，新環境的建立可能需要幾分鐘的時間，視所需相依性的大小而定。 

如果您在提交執行之前未在回合設定中指定環境，系統會為您建立預設環境。

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

同樣地，如果您使用[`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)物件進行定型，您可以直接提交估計工具實例做為執行，而不需要指定環境。 `Estimator` 物件已封裝環境和計算目標。


## <a name="add-packages-to-an-environment"></a>將套件新增至環境

將套件新增至具有 Conda、pip 或私用滾輪檔案的環境。 使用[CondaDependency 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)指定每個套件相依性，並將其新增至環境的 PythonSection。

### <a name="conda-and-pip-packages"></a>Conda 和 pip 套件

如果 Conda 封裝存放庫中有可用的套件，建議使用 Conda over pip 安裝。 原因是 Conda 套件通常會隨附預先建立的二進位檔，讓安裝更可靠。

下列範例會將 `scikit-learn`（特別是版本0.21.3）和 `pillow` 封裝加入至環境，分別 `myenv` [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-)和[`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-)方法。

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>私人滾輪檔案

您可以使用私人 pip 滾輪檔案，方法是先使用靜態[`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-)方法將它上傳至您的工作區儲存體，然後再捕捉儲存體 url，再將 URL 傳遞給 `add_pip_package()` 方法

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>管理環境

管理環境，讓您可以跨計算目標和工作區的其他使用者來更新、追蹤及重複使用它們。

### <a name="register-environments"></a>註冊環境

當您提交執行或部署 web 服務時，環境會自動向您的工作區註冊。 您也可以使用[register （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-)方法，手動註冊環境。 這項作業會讓環境進入在雲端中追蹤和設定版本的實體，並可在工作區使用者之間共用。

下列程式碼會在工作區中註冊環境 `myenv`，`ws`。

```python
myenv.register(workspace=ws)
```

第一次使用時，在訓練或部署中，環境會向工作區註冊，並建立並部署在計算目標上。 服務會快取環境。 重複使用快取的環境所花的時間比使用新的服務或已更新的環境少很多。

### <a name="get-existing-environments"></a>取得現有的環境

環境類別提供的方法，可讓您依名稱、清單或特定定型執行來抓取工作區中的現有環境，以進行疑難排解或審核，以及重現性。

#### <a name="view-list-of-environments"></a>查看環境清單

使用[`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)查看工作區中的環境，然後選取其中一個以重複使用。

#### <a name="get-environment-by-name"></a>依名稱取得環境

您也可以依名稱和版本取得特定的環境。
下列程式碼會使用[get （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-)方法來抓取環境的版本 `1`，`myenv` 在 `ws` 工作區上。

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>訓練執行特定環境

若要在定型完成之後，取得用於特定執行的環境，請使用 Run 類別中的[get_environment （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)方法。

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>更新現有的環境

如果您對現有的環境進行變更（例如新增 Python 套件），則會在您提交執行、部署模型或手動註冊環境時，建立新的環境版本。 版本控制可讓您在一段時間後查看環境的變更。

若要更新現有環境的 Python 套件版本，請指定該套件的確切版本號碼。 否則，Azure Machine Learning 將會在建立環境時，重複使用現有的環境與套件版本。

### <a name="debug-the-image-build"></a>對映射組建進行 Debug

這個範例會使用[build （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-)方法以手動方式將環境建立為 Docker 映射，並使用[wait_for_completion （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)監視映射組建中的輸出記錄。 然後，建立的映射會顯示在 [工作區 Azure Container Registry] 底下，這對進行調試很有説明。

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker 和環境

 Azure Machine Learning `Environments` 類別的[DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) ，可讓您自訂和控制定型執行執行所在的客體作業系統。

當您 `enable` Docker 時，服務會建立 Docker 映射，並在該 Docker 容器中建立具有您規格的 Python 環境。 這會為您的定型執行提供額外的隔離和重現性。

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

建立之後，Docker 映射預設會出現在與工作區相關聯的 Azure Container Registry 中。  存放庫名稱的形式為*azureml/azureml_\<uuid\>* 。 唯一識別碼（*uuid*）元件會對應到從環境設定計算出來的雜湊。 這可讓服務判斷對應至指定環境的映射是否已存在，以供重複使用。

此外，服務會自動使用其中一個以 Ubuntu Linux 為基礎的[基底映射](https://github.com/Azure/AzureML-Containers)，並安裝指定的 Python 套件。 基底映射具有 CPU 和 GPU 版本。 Azure Machine Learning 會自動偵測要使用哪個版本。

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

> [!NOTE]
> 如果您在使用自訂 Docker 映射時指定 `environment.python.user_managed_dependencies=False`，服務會在映射中建立 Conda 環境，並在該環境中執行此工作，而不是使用您可能已安裝在基底映射上的 Python 程式庫。 將參數設定為 `True`，以使用您自己安裝的套件。

## <a name="using-environments-for-training"></a>使用環境進行定型

若要提交定型回合，您需要將您的環境、[計算目標](concept-compute-target.md)和訓練 Python 腳本結合成執行設定;用於提交執行的包裝函式物件。

當您提交定型回合時，新環境的建立可能需要幾分鐘的時間，視所需相依性的大小而定。 服務會快取環境，因此只要環境定義維持不變，就只會產生一次完整的安裝時間。

下列本機腳本執行範例顯示您要使用[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)作為包裝函式物件的位置。

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> 若要停用執行歷程記錄或執行快照集，請使用 [`ScriptRunConfig.run_config.history`] 底下的設定。

如果您未在回合設定中指定環境，服務會在您提交執行時為您建立預設環境。

### <a name="train-with-an-estimator"></a>使用預估器來定型

如果您使用[估計工具](how-to-train-ml-models.md)進行定型，您可以直接提交估計工具實例，因為它已經封裝了環境和計算目標。

下列程式碼會在 scikit-learn 學習模型的遠端計算上使用單一節點定型執行的估計工具，並假設先前建立的計算目標物件、`compute_target` 和資料存放區物件 `ds`。

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>使用 web 服務部署的環境

將您的模型部署為 web 服務時，您可以使用環境。 這會啟用可重現且已連線的工作流程，讓您在定型和推斷計算中使用完全相同的程式庫來定型、測試及部署模型。

若要部署 web 服務，請在您的部署物件中結合環境、推斷計算、評分腳本和已註冊的模型、[部署（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)。 深入瞭解如何[部署 web 服務](how-to-deploy-and-where.md)。

在此範例中，假設您已完成定型回合，並想要將該模型部署到 Azure 容器實例（ACI）。 建立 web 服務時，會將模型和計分檔案掛接在影像上，並將 Azure Machine Learning 推斷堆疊加入至映射。

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Notebook 範例

這個[範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)會在本文中示範的概念和方法上展開。

## <a name="next-steps"></a>後續步驟

* [教學課程：將模型定型](tutorial-train-models-with-aml.md)使用受控計算目標來定型模型。
* 擁有定型的模型之後，請了解[部署模型的方式和位置](how-to-deploy-and-where.md)。
* 查看[環境類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)SDK 參考。
