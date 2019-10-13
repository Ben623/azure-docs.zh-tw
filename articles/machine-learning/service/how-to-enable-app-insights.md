---
title: 從 Machine Learning web 服務端點監視及收集資料
titleSuffix: Azure Machine Learning
description: 使用 Azure 應用程式 Insights 監視以 Azure Machine Learning 部署的 web 服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 10/11/2019
ms.custom: seoapril2019
ms.openlocfilehash: c16b6d769aa191b0e8ac86768a7eafd35ccbc3b9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301021"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>從 ML web 服務端點監視及收集資料

在本文中，您將瞭解如何藉由啟用 Azure 應用程式 Insights，從 Azure Kubernetes Service （AKS）或 Azure 容器實例（ACI）中部署至 web 服務端點的模型收集資料並加以監視。 除了收集端點的輸入資料和回應之外，您還可以監視：
* 要求速率、回應時間和失敗率。
* 相依性速率、回應時間和失敗率。
* 例外狀況。

[深入瞭解 Azure 應用程式 Insights](../../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 已安裝 Azure Machine Learning 工作區、包含指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 若要了解如何取得這些必要條件，請參閱[如何設定開發環境](how-to-configure-environment.md)。
* 要部署至 Azure Kubernetes Service (AKS) 或 Azure Container 執行個體 (ACI) 的已訓練機器學習模型。 如果您沒有模型，請參閱[將影像分類模型定型](tutorial-train-models-with-aml.md)教學課程。

## <a name="web-service-input-and-response-data"></a>Web 服務輸入和回應資料

服務的輸入和回應-對應至 ML 模型的輸入及其預測，會記錄到訊息 `"model_data_collection"` 下的 Azure 應用程式 Insights 追蹤。 您可以直接查詢 Azure 應用程式深入解析以存取此資料，或設定[連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)至儲存體帳戶，以延長保留期或進一步處理。 然後，您可以在 Azure ML 服務中使用模型資料來設定標籤、重新定型、可解釋性、資料分析或其他用途。 

## <a name="use-the-azure-portal-to-configure"></a>使用 Azure 入口網站設定

您可以在 Azure 入口網站中啟用和停用 Azure 應用程式深入解析。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的工作區。

1. 在 [**部署**] 索引標籤上，選取您要啟用 Azure 應用程式深入解析的服務。

   [![[部署] 索引標籤上的服務清單](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. 選取 [編輯]。

   [![[編輯] 按鈕](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. 在 [進階設定] 中，選取 [啟用 AppInsights 診斷] 核取選項。

   [![已選取啟用診斷的核取方塊](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 選取螢幕底部的 [更新] 以套用變更。 

### <a name="disable"></a>停用
1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的工作區。
1. 選取 [**部署**]，選取服務，然後選取 [**編輯**]。

   [![使用 [編輯] 按鈕](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在 [進階設定] 中，清除 [啟用 AppInsights 診斷] 核取選項。 

   [![已清除啟用診斷的核取方塊](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 選取螢幕底部的 [更新] 以套用變更。 
 
## <a name="use-python-sdk-to-configure"></a>使用 Python SDK 設定 

### <a name="update-a-deployed-service"></a>更新已部署的服務
1. 識別您工作區中的服務。 `ws` 的值是工作區的名稱。

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新您的服務，並啟用 Azure 應用程式深入解析。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>記錄您服務中的自訂追蹤
如果您想要記錄自訂追蹤，請依循[部署方式及位置](how-to-deploy-and-where.md)文件中的 AKS 或 ACI 標準部署程序。 然後使用下列步驟：

1. 新增 print 陳述式以更新評分檔案。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新服務組態。
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 建置映像並將其部署在 [AKS](how-to-deploy-to-aks.md) 或 [ACI](how-to-deploy-to-aci.md) 上。  

### <a name="disable-tracking-in-python"></a>在 Python 中停用追蹤

若要停用 Azure 應用程式 Insights，請使用下列程式碼：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="evaluate-data"></a>評估資料
您的服務資料會儲存在您的 Azure 應用程式 Insights 帳戶中，與 Azure Machine Learning 相同的資源群組中。
若要檢視：
1. 前往[Azure 入口網站](https://portal.azure.com)中的 Machine Learning 服務工作區。 按一下 [Azure 應用程式深入解析] 連結。

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. 選取 [概觀] 索引標籤以查看一組您服務的基本計量。

   [![概觀](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. 若要查看您的 web 服務輸入和回應承載，請選取 [**分析**]
1. 在 [架構] 區段中，選取 [**追蹤**]，並以 `"model_data_collection"` 的訊息篩選追蹤。 在自訂維度中，您可以看到輸入、預測和其他相關的詳細資料。

   [@no__t 1Model 的資料](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. 若要查看您的自訂追蹤，請選取 [Analytics]。
4. 在 [結構描述] 區段中，選取 [追蹤]。 然後選取 [執行] 來執行查詢。 資料應該會以資料表格式出現，且應該會對應至您評分檔案中的自訂呼叫。 

   [![自訂追蹤](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

若要深入瞭解如何使用 Azure 應用程式深入解析，請參閱[什麼是 Application Insights？](../../azure-monitor/app/app-insights-overview.md)。

## <a name="export-data-for-further-processing-and-longer-retention"></a>匯出資料以進行進一步的處理和較長的保留

您可以使用 Azure 應用程式 Insights 的[連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)，將訊息傳送至支援的儲存體帳戶，其中可以設定較長的保留期。 @No__t 0 的訊息會以 JSON 格式儲存，而且可以輕鬆地剖析以解壓縮模型資料。 Azure Data Factory、Azure ML 管線或其他資料處理工具可以視需要用來轉換資料。 當您轉換資料之後，您可以使用 Azure Machine Learning 服務工作區將它註冊為資料集。

   [![Continuous 匯出](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>範例筆記本

Ipynb 筆記本中的「[啟用-應用程式深入](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb)解析」會示範本文中的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* 瞭解[如何將模型部署到 Azure Kubernetes Service](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service)叢集，或如何將模型部署到[Azure 容器實例](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance)，以將您的模型部署至 web 服務端點，並啟用 Azure 應用程式 Insights 以利用資料收集和端點監測.
* 請參閱 @no__t 0MLOps：使用 Azure Machine Learning @ no__t-0 來管理、部署及監視模型，以深入瞭解如何運用在生產環境中從模型收集的資料。 這類資料可協助您持續改善機器學習程式。 
