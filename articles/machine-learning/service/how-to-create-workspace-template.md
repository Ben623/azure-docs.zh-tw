---
title: 使用 Azure Resource Manager 範本來建立工作區
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Resource Manager 範本建立新的 Azure Machine Learning 服務工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 2c5491bab9b45df11c2fe81aa933a1a34c49a41b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205927"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>使用 Azure Resource Manager 範本來建立 Azure 機器學習服務工作區

在本文中，您將了解使用 Azure Resource Manager 範本建立 Azure Machine Learning 服務工作區的數種方式。 Resource Manager 範本可讓您輕鬆地以單一、協調的作業建立資源。 範本是 JSON 文件，其定義部署所需的資源。 它也可以指定部署參數。 參數用來在使用範本時提供輸入值。

如需詳細資訊，請參閱 [使用 Azure Resource Manager 範本部署應用程式](../../azure-resource-manager/resource-group-template-deploy.md)。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 如果您沒有訂用帳戶，可以[試用免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* 若要從 CLI 使用範本，您需要 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="resource-manager-template"></a>Resource Manager 範本

下列 Resource Manager 範本可以用來建立 Azure 機器學習服務工作區和相關聯的 Azure 資源中：

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

此範本會建立下列 Azure 服務：

* Azure 資源群組
* Azure 儲存體帳戶
* Azure 金鑰保存庫
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning 工作區

資源群組是保存服務的容器。 各種服務是 Azure Machine Learning 工作區的必要項。

範例範本有兩個參數：

* **位置**是將建立資源群組和服務的位置。

    範本將會針對大部分的資源使用您選取的位置。 例外狀況是 Application Insights 服務，該服務在其他服務可用的所有位置都無法使用。 如果您選取的位置無法使用，服務會建立在美國中南部位置。

* **工作區名稱**是 Azure Machine Learning 工作區的易記名稱。

    其他服務的名稱會隨機產生。

如需範本的詳細資訊，請參閱下列文章：

* [編寫 Azure Resource Manager 範本](../../azure-resource-manager/resource-group-authoring-templates.md)
* [使用 Azure Resource Manager 範本部署應用程式](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.MachineLearningServices resource 類型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 遵循[從自訂範本部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)的步驟。 當您看見 [編輯範本]  畫面時，貼入此文件的範本。
1. 選取 [儲存]  以使用範本。 提供下列資訊，並同意列出的條款及條件：

   * 訂用帳戶：選取要用於這些資源的 Azure 訂用帳戶。
   * 資源群組：選取或建立資源群組以包含服務。
   * 工作區名稱：要用於將建立之Azure Machine Learning 工作區的名稱。 工作區名稱必須介於 3 到 33 個字元之間。 只能包含英數字元和 '-'。
   * 位置：選取將建立資源的位置。

     ![Azure 入口網站中的範本參數](media/how-to-create-workspace-template/template-parameters.png)

如需詳細資訊，請參閱[從自訂範本部署資源](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

這個範例假設您已將範本儲存到目前目錄中名為 `azuredeploy.json` 的檔案：

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/resource-group-template-deploy.md)和[使用 SAS 權杖和 Azure PowerShell 部署私用 Resource Manager 範本](../../azure-resource-manager/resource-manager-powershell-sas-token.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

這個範例假設您已將範本儲存到目前目錄中名為 `azuredeploy.json` 的檔案：

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../../azure-resource-manager/resource-group-template-deploy-cli.md)和[使用 SAS 權杖和 Azure CLI 部署私用 Resource Manager 範本](../../azure-resource-manager/resource-manager-cli-sas-token.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../../azure-resource-manager/resource-group-template-deploy-rest.md)。
* [透過 Visual Studio 建立與部署 Azure 資源群組](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。
