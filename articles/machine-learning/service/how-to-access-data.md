---
title: 存取 Azure 儲存體服務中的資料
titleSuffix: Azure Machine Learning
description: 瞭解如何使用資料存放區在使用 Azure Machine Learning 進行定型期間存取 Azure 儲存體服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ee6ab1ada540f4f664e6782a1fffc63cc7df95e4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928590"
---
# <a name="access-data-in-azure-storage-services"></a>存取 Azure 儲存體服務中的資料
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何透過 Azure Machine Learning 資料存放區，輕鬆地存取 Azure 儲存體服務中的資料。 資料存放區是用來儲存連接資訊，例如您的訂用帳戶識別碼和權杖授權。 使用資料存放區可讓您存取儲存體，而不需要在腳本中硬編碼連接資訊。 您可以從這些[Azure 儲存體解決方案](#matrix)建立資料存放區。 針對不支援的儲存體解決方案，以及在機器學習實驗期間儲存資料輸出成本，建議您將資料移至支援的 Azure 儲存體解決方案。 [瞭解如何移動您的資料](#move)。 

本操作說明示範下列工作的範例：
* 註冊資料存放區
* 從工作區取得資料存放區
* 使用資料存放區上傳和下載資料
* 在定型期間存取資料
* 將資料移至 Azure 儲存體服務

## <a name="prerequisites"></a>必要條件
您將需要
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 具有[Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或[azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 azure 儲存體帳戶。

- [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning studio](https://ml.azure.com/)的存取權。

- Azure Machine Learning 工作區。 
    - 請[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或使用 Python SDK 來使用現有的工作區。

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>建立並註冊資料存放區

當您將 Azure 儲存體解決方案註冊為數據存放區時，會自動在特定工作區中建立該資料存放區。 您可以使用 Python SDK 或 Azure Machine Learning studio 建立資料存放區，並將其註冊至工作區。

### <a name="using-the-python-sdk"></a>使用 Python SDK

所有的暫存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別上，且格式為 register_azure_ *。

您需要填入 register （）方法的資訊，可透過[Azure Machine Learning studio](https://ml.azure.com)和下列步驟來找到。

1. 在左窗格中選取 [**儲存體帳戶**]，然後選擇您要註冊的儲存體帳戶。 
2. [**總覽**] 頁面會提供帳戶名稱和容器或檔案共用名稱等資訊。 
3. 如需驗證資訊，例如帳戶金鑰或 SAS 權杖，請流覽至左側 [**設定**] 窗格底下的 [**帳戶金鑰**]。 

>重大如果您的儲存體帳戶在 VNET 中，則只支援 Azure blob 資料存放區的建立。 設定參數，`grant_workspace_access` `True`，將您的工作區存取權授與您的儲存體帳戶。

下列範例會示範如何將 Azure Blob 容器或 Azure 檔案共用註冊為數據存放區。

+ 針對**Azure Blob 容器資料**存放區，請使用[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    下列程式碼會建立資料存放區（`my_datastore`），並將其註冊至工作空間，`ws`。 此資料存放區會存取 azure 儲存體帳戶上的 Azure blob 容器 `my_blob_container`，`my_storage_account` 使用提供的帳戶金鑰。

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ 針對**Azure 檔案共用資料存放區**，請使用[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

    下列程式碼會建立資料存放區（`my_datastore`），並將其註冊至工作空間，`ws`。 此資料存放區會存取 azure 儲存體帳戶上的 Azure 檔案共用 `my_file_share`，`my_storage_account` 使用提供的帳戶金鑰。

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>儲存體指引

我們建議 Azure Blob 容器。 Standard 和 premium 儲存體都適用于 blob。 雖然較昂貴，但我們建議使用高階儲存體，因為輸送量速度較快，可以改善定型執行的速度，特別是當您針對大型資料集進行定型時。 如需儲存體帳戶的成本資訊，請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

### <a name="using-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio 

在 Azure Machine Learning studio 中的幾個步驟建立新的資料存放區。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。
1. 在左窗格中選取 [**管理**] 底下的 [**資料存放區**]。
1. 選取 [ **+ 新增資料**存放區]。
1. 完成 [新的資料存放區] 表單。 表單會根據 Azure 儲存體類型和驗證類型的選擇，以智慧方式更新。
  
您可以透過[Azure 入口網站](https://portal.azure.com)找到填入表單所需的資訊。 在左窗格中選取 [**儲存體帳戶**]，然後選擇您要註冊的儲存體帳戶。 [**總覽**] 頁面會提供帳戶名稱和容器或檔案共用名稱等資訊。 針對驗證專案（例如帳戶金鑰或 SAS 權杖），流覽至左側 [**設定**] 窗格底下的 [**帳戶金鑰**]。

下列範例示範表單在建立 Azure blob 資料存放區時的外觀。 
    
 ![新的資料存放區](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>從您的工作區取得資料存放區

若要取得在目前工作區中註冊的特定資料存放區，請使用資料存放區類別上的[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)靜態方法：

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要取得向指定工作區註冊的資料存放區清單，您可以使用工作區物件上的[`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)屬性：

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

當您建立工作區時，Azure Blob 容器和 Azure 檔案共用會自動註冊到名為 `workspaceblobstore` 的工作區，並分別 `workspacefilestore`。 這些會儲存 Blob 容器的連線資訊，以及在附加至工作區的儲存體帳戶中布建的檔案共用。 `workspaceblobstore` 會設定為預設資料存放區。

取得工作區的預設資料存放區：

```Python
datastore = ws.get_default_datastore()
```

若要為目前的工作區定義不同的預設資料存放區，請在工作區物件上使用[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)方法：

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>上傳 & 下載資料
下列範例中所述的[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)和[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)方法，是針對[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)類別的特定和運作方式。

### <a name="upload"></a>上傳

 使用 Python SDK 將目錄或個別檔案上傳至資料存放區。

上傳目錄至資料存放區 `datastore`：

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` 參數會指定檔案共用（或 blob 容器）中要上傳的位置。 該位置預設為 `None`，這表示會將資料上傳至根目錄。 否則，如果 `overwrite=True` 會覆寫 `target_path` 的任何現有資料。

或透過 `upload_files()` 方法，將個別檔案清單上傳至資料存放區。

### <a name="download"></a>下載

同樣地，將資料從資料存放區下載至本機檔案系統。

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 參數是要下載資料的本機目錄位置。 若要指定檔案共用 (或 Blob 容器) 中資料夾的路徑以進行下載，請在 `prefix` 中提供該路徑。 如果 `prefix` 是 `None`，表示將會下載檔案共用 (或 Blob 容器) 的所有內容。

<a name="train"></a>
## <a name="access-your-data-during-training"></a>在定型期間存取您的資料

> [!IMPORTANT]
> 使用[Azure Machine Learning 資料集](how-to-create-register-datasets.md)是在定型中存取資料的新建議方式。 資料集提供將表格式資料載入 pandas 或 spark 資料框架的功能，以及從 Azure Blob、Azure 檔案、Azure Data Lake Gen 1、Azure Data Lake Gen 2、Azure SQL、Azure 于 postgresql 下載或掛接任何格式檔案的功能。 深入瞭解[如何使用資料集進行定型](how-to-train-with-datasets.md)。

下表列出指示計算目標如何在執行期間使用資料存放區的方法。 

路|方法|描述|
----|-----|--------
掛接| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| 使用在計算目標上掛接資料存放區。 裝載時，您的資料存放區的所有檔案都可供您的計算目標存取。
下載|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|使用將資料存放區的內容下載至 `path_on_compute`所指定的位置。 <br><br> 此下載會在執行之前進行。
上傳|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| 使用，將檔案從 `path_on_compute` 所指定的位置上傳至您的資料存放區。 <br><br> 此上傳會在您執行之後發生。

若要參考資料存放區中的特定資料夾或檔案，並將其提供給計算目標，請使用資料存放區[`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-)方法。

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 任何指定的 `datastore` 或 `datastore.path` 物件都會解析為格式 `"$AZUREML_DATAREFERENCE_XXXX"`的環境變數名稱，其值代表目標計算上的掛接/下載路徑。 目標計算上的資料存放區路徑可能不會與定型腳本的執行路徑相同。

### <a name="examples"></a>範例 

下列程式碼範例是在定型期間用來存取資料的[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)類別特有的。

`script_params` 是包含 entry_script 參數的字典。 使用它來傳入資料存放區，並描述如何在計算目標上提供資料。 深入瞭解我們的端對端[教學](tutorial-train-models-with-aml.md)課程。

```Python
from azureml.train.estimator import Estimator

# notice '/' is in front, this indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

您也可以將資料存放區清單傳遞至估計工具的 `inputs` 參數，以在資料存放區中裝載或複製資料。 此程式碼範例：
* 在您的定型腳本 `train.py` 執行之前，將 `datastore1` 中的所有內容下載到計算目標
* 在執行 `train.py` 之前，將 `datastore2` 中的資料夾 `'./foo'` 下載到計算目標
* 在您的腳本執行之後，將檔案 `'./bar.pkl'` 從計算目標上傳至 `datastore3`

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>計算和資料存放區矩陣

資料存放區目前支援將連接資訊儲存至下列矩陣中所列的儲存體服務。 此矩陣會顯示不同計算目標和資料存放區案例的可用資料存取功能。 深入瞭解 Azure Machine Learning 的[計算目標](how-to-set-up-training-targets.md#compute-targets-for-training)。

|運算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 地方|[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning Compute |[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管線](concept-ml-pipelines.md)|[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管線](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| 虛擬機器               |[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| 資料轉送                  |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)            |[ML&nbsp;管線](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;管線](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;管線](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake 分析       |N/A                                           |N/A                                           |[ML&nbsp;管線](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 在某些情況下，使用 `as_download()` 而不是 `as_mount()`，可能會有高度反復的大型資料處理程式執行速度較快;這可以驗證 experimentally。

### <a name="accessing-source-code-during-training"></a>在定型期間存取原始程式碼

Azure blob 儲存體的輸送量速度高於 Azure 檔案共用，而且會調整為以平行方式啟動的大量作業。 基於這個理由，我們建議您將執行設定為使用 blob 儲存體來傳送原始程式碼檔案。

下列程式碼範例會在執行設定中指定要用於來來源程式代碼傳輸的 blob 資料存放區。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在評分期間存取資料

Azure Machine Learning 提供數種方式來使用您的模型進行評分。 其中一些方法不會提供資料存放區的存取權。 使用下表瞭解哪些方法可讓您在計分期間存取資料存放區：

| 方法 | 資料存放區存取 | 描述 |
| ----- | :-----: | ----- |
| [批次預測](how-to-run-batch-predictions.md) | ✔ | 以非同步方式對大量資料進行預測。 |
| [Web 服務](how-to-deploy-and-where.md) | &nbsp; | 將模型部署為 web 服務。 |
| [IoT Edge 模組](how-to-deploy-and-where.md) | &nbsp; | 將模型部署到 IoT Edge 裝置。 |

針對 SDK 不提供資料存放區存取權的情況，您可以使用相關的 Azure SDK 來建立自訂程式碼來存取資料。 例如，適用于[Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)是用戶端程式庫，可讓您用來存取儲存在 blob 或檔案中的資料。

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>將資料移至支援的 Azure 儲存體解決方案

Azure Machine Learning 支援從 Azure Blob、Azure 檔案、Azure Data Lake Gen 1、Azure Data Lake Gen 2、Azure SQL、Azure 于 postgresql 存取資料。 針對不支援的儲存體，若要在機器學習實驗期間儲存資料輸出成本，建議您使用 Azure Data Factory，將您的資料移至支援的 Azure 儲存體解決方案。 Azure Data Factory 使用超過80個預先建立的連接器（包括 Azure 資料服務、內部部署資料來源、Amazon S3 和 Redshift 和 Google BigQuery）提供有效率且彈性的資料傳輸，而且不需額外費用。 [遵循逐步指南，使用 Azure Data Factory 來移動資料](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

## <a name="next-steps"></a>後續步驟

* [將模型定型](how-to-train-ml-models.md)。

* [部署模型](how-to-deploy-and-where.md)。
