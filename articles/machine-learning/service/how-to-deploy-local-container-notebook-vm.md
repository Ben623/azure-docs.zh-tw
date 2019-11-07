---
title: 如何將模型部署至筆記本 Vm
titleSuffix: Azure Machine Learning
description: 瞭解如何使用筆記本 Vm，將您的 Azure Machine Learning 模型部署為 web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: d4e37b02b3d7a21546a04c8948fbbfb7262bfa6a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584760"
---
# <a name="deploy-a-model-to-azure-machine-learning-notebook-vms"></a>將模型部署到 Azure Machine Learning 的筆記本 Vm

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

瞭解如何使用 Azure Machine Learning 將模型部署為 Azure Machine Learning 筆記本 VM 上的 web 服務。 如果下列其中一個條件成立，請使用筆記本 Vm：

- 您需要快速部署及驗證模型。
- 您正在測試處於開發狀態的模型。

> [!TIP]
> 從筆記本 VM 上的 Jupyter Notebook 將模型部署至相同 VM 上的 web 服務是_本機部署_。 在此情況下，「本機」電腦是筆記本 VM。 如需部署的詳細資訊，請參閱[使用 Azure Machine Learning 部署模型](how-to-deploy-and-where.md)。

## <a name="prerequisites"></a>必要條件

- 具有執行中筆記本 VM 的 Azure Machine Learning 工作區。 如需詳細資訊，請參閱[設定環境和工作區](tutorial-1st-experiment-sdk-setup.md)。

## <a name="deploy-to-the-notebook-vms"></a>部署至筆記本 Vm

您的筆記本 VM 上會包含示範本機部署的範例筆記本。 使用下列步驟來載入筆記本，並將模型部署為 VM 上的 web 服務：

1. 從[Azure Machine Learning studio](https://ml.azure.com)中，選取您的 Azure Machine Learning 筆記本 vm。

1. 開啟 `samples-*` 子目錄，然後開啟 `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`。 一旦開啟，請執行筆記本。

    ![筆記本上執行中本機服務的螢幕擷取畫面](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. 筆記本會顯示服務執行所在的 URL 和埠。 例如， `https://localhost:6789`。 您也可以執行包含 `print('Local service port: {}'.format(local_service.port))` 的資料格來顯示埠。

    ![執行中本機服務埠的螢幕擷取畫面](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. 若要從筆記本 VM 測試服務，請使用 `https://localhost:<local_service.port>` URL。 若要從遠端用戶端進行測試，請取得在筆記本 VM 上執行之服務的公用 URL。 您可以使用下列公式來判斷公用 URL：`https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`。 例如， `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`。

## <a name="test-the-service"></a>測試服務

若要將範例資料提交至執行中的服務，請使用下列程式碼。 將 `service_url` 的值取代為上一個步驟中的 URL：

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>後續步驟

* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑難排解](how-to-troubleshoot-deployment.md)
* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)