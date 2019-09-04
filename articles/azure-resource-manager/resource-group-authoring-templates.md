---
title: Azure Resource Manager 範本結構和語法 | Microsoft Docs
description: 使用宣告式 JSON 語法描述 Azure Resource Manager 範本的結構和屬性。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 53b2f9783b33c859ca2c5de5f35353b8482ea5c7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275128"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>了解 Azure Resource Manager 範本的結構和語法

本文說明 Azure Resource Manager 範本的結構。 它會呈現範本的不同區段，以及這些區段中可用的屬性。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。

本文適用于熟悉 Resource Manager 範本的使用者。 它會提供有關範本結構和語法的詳細資訊。 如果您想要建立範本的簡介, 請參閱[建立您的第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md)。

## <a name="template-format"></a>範本格式

在最簡單的結構中，範本具有下列元素：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| 元素名稱 | 必要項 | 描述 |
|:--- |:--- |:--- |
| $schema |是 |JSON 結構描述檔案的位置，說明範本語言的版本。<br><br> 針對資源群組部署，使用：`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>針對訂用帳戶部署，使用：`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |是 |範本版本 (例如 1.0.0.0)。 您可以為此元素提供任何值。 使用此值在範本中記載重大變更。 使用範本部署資源時，這個值可用來確定使用的是正確的範本。 |
| apiProfile |否 | API 版本, 可作為資源類型的 API 版本集合。 使用此值可避免必須為範本中的每個資源指定 API 版本。 當您指定 API 設定檔版本, 但未指定資源類型的 API 版本時, Resource Manager 會使用設定檔中所定義之該資源類型的 API 版本。<br><br>將範本部署到不同的環境 (例如 Azure Stack 和全域 Azure) 時, API 配置檔案屬性特別有用。 使用 API 設定檔版本, 以確保您的範本會自動使用兩個環境中支援的版本。 如需目前的 API 設定檔版本清單和設定檔中定義的資源 API 版本, 請參閱[API 設定檔](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)。<br><br>如需詳細資訊, 請參閱[使用 API 設定檔來追蹤版本](templates-cloud-consistency.md#track-versions-using-api-profiles)。 |
| [parameters](#parameters) |否 |執行部署以自訂資源部署時所提供的值。 |
| [variables](#variables) |否 |範本中做為 JSON 片段以簡化範本語言運算式的值。 |
| [函式](#functions) |否 |範本中可用的使用者定義函式。 |
| [resources](#resources) |是 |在資源群組或訂用帳戶中部署或更新的資源類型。 |
| [outputs](#outputs) |否 |部署後傳回的值。 |

每個元素都有可以設定的屬性。 本文將詳細說明範本的各個區段。

## <a name="parameters"></a>參數

在範本的 parameters 區段中，您會指定可在部署資源時輸入的值。 提供針對特定環境 (例如開發、測試和生產環境) 量身訂做的參數值，可讓您自訂部署。 您不必在範本中提供參數，但若沒有參數，您的範本一律會部署具有相同名稱、位置和屬性的相同資源。

範本中限制使用 256 個參數。 您可以使用包含多個屬性的物件，以減少參數數目，如本文所示。

### <a name="available-properties"></a>可用屬性

參數的可用屬性包括:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>" 
    }
  }
}
```

| 元素名稱 | 必要項 | 描述 |
|:--- |:--- |:--- |
| parameterName |是 |參數名稱。 必須是有效的 JavaScript 識別碼。 |
| Type |是 |參數值類型。 允許的類型和值為 **string**、**securestring**、**int**、**bool**、**object**、**secureObject**，以及 **array**。 |
| defaultValue |否 |如果未提供參數值，則會使用參數的預設值。 |
| allowedValues |否 |參數的允許值陣列，確保提供正確的值。 |
| minValue |否 |int 類型參數的最小值，含此值。 |
| maxValue |否 |int 類型參數的最大值，含此值。 |
| minLength |否 |字串、securestring 及陣列類型參數長度的最小值，含此值。 |
| maxLength |否 |字串、securestring 及陣列類型參數長度的最大值，含此值。 |
| description |否 |透過入口網站向使用者顯示的參數說明。 如需詳細資訊，請參閱[範本中的註解](#comments)。 |

### <a name="define-and-use-a-parameter"></a>定義及使用參數

下列範例顯示簡單的參數定義。 其定義參數的名稱，並指定其需要字串值。 參數僅接受對其預期用途有意義的值。 在部署期間未提供任何值時，會指定預設值。 最後，參數包含其用途描述。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

在範本中，您可以參考使用下列語法的參數值：

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>使用參數的範本函式

為參數指定預設值時，您可以使用大部分的範本函式。 您可以使用另一個參數以建立預設值。 下列範本示範如何以預設值使用函式：

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

您無法使用參數區段中的 `reference` 函式。 參數會在部署之前受到評估，因此，`reference` 函式無法取得資源的執行階段狀態。 

### <a name="objects-as-parameters"></a>作為參數的物件

藉由將相關值以物件的方式傳遞，可以更輕鬆地進行組織。 此方法也會減少範本中的參數數目。

部署期間，請在範本中定義參數並指定 JSON 物件，而非單一值。 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

然後，使用點運算子來參考參數的子屬性。

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>參數範例範本

這些範例範本示範使用參數的一些情況。 部署這些參數以測試如何在不同的情況下處理參數。

|範本  |描述  |
|---------|---------|
|[具有預設值之帶有函式的參數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 示範定義參數的預設值時，如何使用範本函式。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |
|[參數物件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 示範如何針對參數使用物件。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |

## <a name="variables"></a>變數

在 variables 區段中，您會建構可用於整個範本中的值。 您不需要定義變數，但它們通常會經由減少複雜運算式來簡化您的範本。

### <a name="available-definitions"></a>可用定義

下列範例顯示定義變數的可用選項:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

如需使用`copy`來為變數建立數個值的詳細資訊, 請參閱[變數反復](resource-group-create-multiple.md#variable-iteration)專案。

### <a name="define-and-use-a-variable"></a>定義及使用變數

下列範例顯示變數定義。 它會建立儲存體帳戶名稱的字串值。 它會使用數個範本函式來取得參數值, 並將其串連為唯一字串。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

您可在定義資源時使用此變數。

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>設定變數

您可以使用複雜 JSON 類型來定義環境的相關值。

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

在 parameters 中，您可建立一個值來表示所要使用的組態值。

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

您可利用下列方式來擷取目前的設定：

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>變數範例範本

這些範例範本會示範使用變數的一些情況。 部署這些範本以測試如何在不同的情況下處理變數。 

|範本  |描述  |
|---------|---------|
| [變數定義](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 示範不同類型的變數。 範本不會部署任何資源。 它會建構變數值並傳回這些值。 |
| [組態變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 示範如何使用可定義組態值的變數。 範本不會部署任何資源。 它會建構變數值並傳回這些值。 |
| [網路安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)和[參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 建構正確格式的陣列，以便將安全性規則指派給網路安全性群組。 |


## <a name="functions"></a>Functions

在您的範本內，您可以建立自己的函式。 這些函式可供您在範本中使用。 您通常會定義不想在整個範本中重複使用的複雜運算式。 您會從範本中支援的運算式和[函式](resource-group-template-functions.md)建立使用者定義的函式。

在定義使用者函式時，有一些限制：

* 此函式無法存取變數。
* 此函式只能使用函式中定義的參數。 當您在使用者定義函數中使用[parameters](resource-group-template-functions-deployment.md#parameters)函式時, 會限制為該函數的參數。
* 此函式無法呼叫其他的使用者定義函式。
* 此函式不能使用[參考函式](resource-group-template-functions-resource.md#reference)。
* 函式的參數不能有預設值。

您的函式需要命名空間值，以避免範本函式發生命名衝突。 下列範例說明可傳回儲存體帳戶名稱的函式：

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

您呼叫函式的方式：

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>資源
在資源區段中，您會定義要部署或更新的資源。

### <a name="available-properties"></a>可用屬性

您會定義結構如下的資源：

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 元素名稱 | 必要項 | 描述 |
|:--- |:--- |:--- |
| condition (條件) | 否 | 布林值，指出是否會在此部署期間佈建資源。 若為 `true`，就會在部署期間建立資源。 若為 `false`，則會略過此部署的資源。 請參閱[條件式部署](conditional-resource-deployment.md)。 |
| apiVersion |是 |要用來建立資源的 REST API 版本。 若要判斷可用的值, 請參閱[範本參考](/azure/templates/)。 |
| Type |是 |資源類型。 這個值是資源提供者的命名空間與資源類型的組合 (例如 **Microsoft.Storage/storageAccounts**)。 若要判斷可用的值, 請參閱[範本參考](/azure/templates/)。 對於子資源, 類型的格式取決於它是內嵌在父資源內, 還是在父資源外部定義。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |
| name |是 |資源名稱。 此名稱必須遵循在 RFC3986 中定義的 URI 元件限制。 此外，將資源名稱公開到外部合作對象的 Azure 服務會驗證該名稱，確定並非嘗試詐騙其他身分識別。 對於子資源, 名稱的格式取決於它是內嵌在父資源內, 還是在父資源外部定義。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |
| 位置 |視情況而異 |所提供資源的支援地理位置。 您可以選取任何可用的位置，但通常選擇接近您的使用者的位置很合理。 通常，將彼此互動的資源放在相同區域也合乎常理。 大部分的資源類型都需要有位置，但某些類型 (例如角色指派) 不需要位置。 請參閱[設定資源位置](resource-location.md) |
| tags |否 |與資源相關聯的標記。 套用標籤，既可以邏輯方式組織訂用帳戶中的資源。 |
| comments |否 |您在範本中記錄資源的註解。 如需詳細資訊，請參閱[範本中的註解](resource-group-authoring-templates.md#comments)。 |
| 複製 |否 |如果需要多個執行個體，要建立的資源數目。 預設模式為平行。 如果您不想要同時部署所有或某些資源，請指定序列模式。 如需詳細資訊，請參閱[在 Azure Resource Manager 中建立資源的數個執行個體](resource-group-create-multiple.md)。 |
| dependsOn |否 |在部署這項資源之前必須部署的資源。 Resource Manager 會評估資源之間的相依性，並依正確的順序進行部署。 資源若不互相依賴，則會平行部署資源。 值可以是以逗號分隔的資源名稱或資源唯一識別碼清單。 只會列出此範本中已部署的資源。 此範本中未定義的資源必須已經存在。 避免加入不必要的相依性，因為可能會降低部署速度並產生循環相依性。 如需設定相依性的指引，請參閱[定義 Azure Resource Manager 範本中的相依性](resource-group-define-dependencies.md)。 |
| properties |否 |資源特定的組態設定。 屬性的值和您在 REST API 作業 (PUT 方法) 要求主體中提供來建立資源的值是一樣的。 您也可以指定複本陣列，以建立屬性的數個執行個體。 若要判斷可用的值, 請參閱[範本參考](/azure/templates/)。 |
| SKU | 否 | 某些資源允許以值定義要部署的 SKU。 例如，您可以指定儲存體帳戶的備援類型。 |
| kind | 否 | 某些資源允許以值定義您所部署的資源類型。 例如，您可以指定要建立的 Cosmos DB 類型。 |
| 計劃 | 否 | 某些資源允許以值定義要部署的計劃。 例如，您可以指定虛擬機器的 Marketplace 映像。 | 
| 資源 |否 |與正在定義的資源相依的下層資源。 只提供父資源的結構描述允許的資源類型。 沒有隱含父資源的相依性。 您必須明確定義該相依性。 請參閱[設定子資源的名稱和類型](child-resource-name-type.md)。 |

### <a name="resource-names"></a>資源名稱

通常，您會使用 Resource Manager 中的三種資源名稱︰

* 必須是唯一的資源名稱。
* 不需要是唯一的資源名稱，但是您選擇提供的名稱可協助您識別資源。
* 可以是一般的資源名稱。

針對具有資料存取端點的任何資源類型, 提供**唯一的資源名稱**。 一些需要唯一名稱的常見資源類型包括︰

* Azure 儲存體<sup>1</sup> 
* Azure App Service 中的 Web Apps 功能
* [SQL Server]
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure 流量管理員
* Azure 搜尋服務
* Azure HDInsight

<sup>1</sup> 儲存體帳戶名稱也必須是小寫、長度不超過 24 個字元，而且沒有任何連字號。

設定名稱時，您可以手動建立唯一的名稱或使用 [uniqueString()](resource-group-template-functions-string.md#uniquestring) 函式來產生名稱。 您也可能想要將首碼或尾碼新增至 **uniqueString** 結果。 修改唯一的名稱可協助您更輕鬆地識別名稱的資源類型。 例如，您可以使用下列變數來產生儲存體帳戶的唯一名稱：

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

針對某些資源類型, 您可能會想要提供**識別的名稱**, 但名稱不需要是唯一的。 針對這些資源類型, 提供描述其使用或特性的名稱。

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

對於大部分透過不同資源存取的資源類型, 您可以使用在範本中硬式編碼的**一般名稱**。 例如，您可以在 SQL Server 上設定防火牆規則的標準、一般名稱︰

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

## <a name="outputs"></a>outputs

在輸出區段中，您可以指定從部署傳回的值。 通常, 您會從已部署的資源傳回值。

### <a name="available-properties"></a>可用屬性

下列範例顯示輸出定義的結構：

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| 元素名稱 | 必要項 | 描述 |
|:--- |:--- |:--- |
| outputName |是 |輸出值的名稱。 必須是有效的 JavaScript 識別碼。 |
| condition (條件) |否 | 布林值，指出是否傳回此輸出值。 當為 `true` 時，該值會包含在部署的輸出中。 若為 `false`，則會略過此部署的輸出值。 未指定時，預設值為 `true`。 |
| Type |是 |輸出值的類型。 輸出值支援與範本輸入參數相同的類型。 如果您針對輸出類型指定**securestring** , 此值不會顯示在部署歷程記錄中, 而且無法從另一個範本抓取。 若要在多個範本中使用秘密值, 請將密碼儲存在 Key Vault 中, 並在參數檔案中參考密碼。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](resource-manager-keyvault-parameter.md)。 |
| value |是 |評估並傳回做為輸出值的範本語言運算式。 |

### <a name="define-and-use-output-values"></a>定義和使用輸出值

下列範例示範如何傳回公用 IP 位址的資源識別碼：

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

下一個範例示範如何根據是否部署了新的 IP 位址，來有條件地傳回公用 IP 位址的資源識別碼：

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

如需條件式輸出的簡單範例, 請參閱[條件式輸出範本](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

部署之後，您就可以使用指令碼來擷取值。 對於 PowerShell，請使用：

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

對於 Azure CLI，請使用：

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

您也可以使用 [reference](resource-group-template-functions-resource.md#reference) 函式，從連結的範本中擷取輸出值。 若要從連結的範本取得輸出值，請使用如下語法擷取屬性值：`"[reference('deploymentName').outputs.propertyName.value]"`。

從連結的範本取得輸出屬性時，屬性名稱不能包含連字號。

下列範例示範如何藉由從連結的範本中抓取值來設定負載平衡器上的 IP 位址。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

您無法在[巢狀範本](resource-group-linked-templates.md#link-or-nest-a-template)的輸出區段中使用 `reference` 函式。 若要傳回巢狀範本中已部署資源的值，請將巢狀範本轉換成連結範本。

### <a name="output-example-templates"></a>輸出範例範本

|範本  |描述  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 建立複雜的變數，並輸出那些值。 不會部署任何資源。 |
|[公用 IP 位址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 建立公用 IP 位址，並輸出資源識別碼。 |
|[負載平衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 上述範本的連結。 建立負載平衡器時，在輸出中使用資源識別碼。 |


<a id="comments" />

## <a name="comments-and-metadata"></a>註解與中繼資料

有幾個選項可將註解與中繼資料新增到您的範本中。

幾乎在範本的任何位置都可以新增 `metadata` 物件。 Resource Manager 會略過該物件，但您的 JSON 編輯器可能會警告您該屬性無效。 在物件中，定義您需要的屬性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

對於**參數**，新增 `description` 屬性的 `metadata` 物件。

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

透過入口網站部署範本時，您在描述中提供的文字會自動做為該參數的提示。

![顯示參數提示](./media/resource-group-authoring-templates/show-parameter-tip.png)

對於**資源**，新增 `comments` 項目或中繼資料物件。 下列範例顯示註解項目和中繼資料物件。

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

對於**輸出**，將中繼資料物件新增至輸出值。

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

您無法將中繼資料物件新增至使用者定義函式。

對於內嵌註解，您可以使用 `//`，但這個語法不適用於所有工具。 您無法使用 Azure CLI 來部署有內嵌註解的範本。 您無法使用入口網站範本編輯器來處理有內嵌註解的範本。 如果您新增這種樣式的註解，請務必使用支援內嵌 JSON 註解的工具。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

在 VS Code 中，您可以設定語言模式為含註解的 JSON。 內嵌註解不再被標示為無效。 若要變更模式：

1. 開啟語言模式選取項目 (Ctrl+K M)

1. 選取 [含註解的 JSON]。

   ![選取語言模式](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>後續步驟
* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
* 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 若要在部署期間合併數個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 如需建立範本的建議，請參閱 [Azure Resource Manager 範本最佳做法](template-best-practices.md)。
* 如需對於建立可跨越全部 Azure 環境和 Azure Stack 使用的 Resource Manager 範本相關建議，請參閱[開發針對雲端一致性的 Azure Resource Manager 範本](templates-cloud-consistency.md)。
