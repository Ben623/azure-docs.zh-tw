---
title: Azure Resource Manager 範本函式 - 部署 | Microsoft Docs
description: 描述 Azure Resource Manager 範本中用來擷取部署資訊的函式。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2019
ms.author: tomfitz
ms.openlocfilehash: c5bd40741ec0fe047f98b4b4431819d90e188385
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66128660"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 範本的部署函式 

資源管理員提供下列函式，以從與部署相關的範本和值的區段中取得值：

* [部署](#deployment)
* [參數](#parameters)
* [變數](#variables)

若要從資源、資源群組或訂用帳戶中取得值，請參閱 [資源函式](resource-group-template-functions-resource.md)。

<a id="deployment" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment"></a>deployment
`deployment()`

傳回目前部署作業的相關資訊。

### <a name="return-value"></a>傳回值

此函式會傳回部署期間所傳遞的物件。 視部署物件是以連結或內嵌物件形式傳遞，所傳回物件中的屬性將有所不同。 部署物件以內嵌形式傳遞時 (例如使用 Azure PowerShell 中的 **-TemplateFile** 參數指向本機檔案時)，所傳回的物件為下列格式：

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

部署物件以連結形式傳遞時 (例如使用 **-TemplateUri** 參數指向遠端檔案時)，所傳回的物件為下列格式： 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

當您[部署到 Azure 訂用帳戶](deploy-to-subscription.md)而不是資源群組時，傳回的物件包含 `location` 屬性。 部署本機範本或外部範本時，包含 location 屬性。

### <a name="remarks"></a>備註

您可以上層範本的 URI 作為基礎，使用部署() 連結至另一個範本。

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

如果您在入口網站中重新部署部署歷程記錄中的範本，此範本會部署為本機檔案。 `templateLink` 屬性不會在部署函式中傳回。 如果您的範本依賴 `templateLink` 來建構另一個範本的連結，請勿使用入口網站來重新部署。 請改用您原先用來部署範本的命令。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json)會傳回部署物件︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

上述範例會傳回下列物件︰

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

如需使用部署函式的訂用帳戶層級範本，請參閱[訂用帳戶部署函式](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json)。 它會使用 `az deployment create` 或 `New-AzDeployment` 命令進行部署。

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

傳回參數值。 指定的參數名稱必須定義於範本的 parameters 區段中。

### <a name="parameters"></a>參數

| 參數 | 必要項 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |string |要傳回的參數名稱。 |

### <a name="return-value"></a>傳回值

指定參數的值。

### <a name="remarks"></a>備註

一般而言，您可以使用參數來設定資源的值。 下列範例會將網站的名稱設定為部署期間所傳入的參數值。

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json)顯示 parameters 函式的簡化用法。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| Name | 類型 | 值 |
| ---- | ---- | ----- |
| stringOutput | String | option 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | option 1 |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

傳回變數的值。 指定的變數名稱必須定義於範本的 variables 區段中。

### <a name="parameters"></a>參數

| 參數 | 必要項 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| variableName |是 |String |要傳回的變數名稱。 |

### <a name="return-value"></a>傳回值

指定變數的值。

### <a name="remarks"></a>備註

一般而言，您可以使用變數將範本簡化，方法是僅建構一次複雜的值。 下列範例會建構儲存體帳戶的唯一名稱。

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json)會傳回不同的變數值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>後續步驟
* 如需有關 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要合併數個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 若要依指定的次數重複建立資源類型，請參閱 [在 Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](resource-group-template-deploy.md)。

