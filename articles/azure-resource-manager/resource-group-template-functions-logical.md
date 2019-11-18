---
title: 範本函式-邏輯
description: 描述 Azure Resource Manager 範本中用來決定邏輯值的函式。
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: df8433d167a166fe94d965f81e42cd0b3e8f0e54
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150701"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 範本的邏輯函式

Resource Manager 提供了幾個用來在範本中進行比較的函式。

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>和

`and(arg1, arg2, ...)`

檢查所有參數值是否為 true。

### <a name="parameters"></a>parameters

| 參數 | 必要 | 在系統提示您進行確認時，輸入 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |yes |布林值 |要檢查是否為 ture 的第一個值。 |
| arg2 |yes |布林值 |要檢查是否為 ture 的第二個值。 |
| 其他引數 |否 |布林值 |要檢查是否為 true 的其他引數。 |

### <a name="return-value"></a>傳回值

如果所有值都是 true，則傳回 **True**，否則會傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)會示範如何使用邏輯函式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前述範例的輸出為：

| 名稱 | 在系統提示您進行確認時，輸入 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |

## <a name="bool"></a>布林

`bool(arg1)`

將參數轉換為布林值。

### <a name="parameters"></a>parameters

| 參數 | 必要 | 在系統提示您進行確認時，輸入 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |yes |字串或整數 |要轉換為布林值的值。 |

### <a name="return-value"></a>傳回值
轉換值的布林值。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)顯示如何搭配使用 bool 與字串或整數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

先前範例中具有預設值的輸出如下：

| 名稱 | 在系統提示您進行確認時，輸入 | 值 |
| ---- | ---- | ----- |
| trueString | Bool | true |
| falseString | Bool | False |
| trueInt | Bool | true |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

根據條件是 true 或 false 傳回值。

### <a name="parameters"></a>parameters

| 參數 | 必要 | 在系統提示您進行確認時，輸入 | 描述 |
|:--- |:--- |:--- |:--- |
| condition |yes |布林值 |要檢查其是否為 true 或 false 的值。 |
| trueValue |yes | 字串、int、物件或陣列 |條件為 true 時，傳回的值。 |
| falseValue |yes | 字串、int、物件或陣列 |條件為 false 時，傳回的值。 |

### <a name="return-value"></a>傳回值

當第一個參數是 **True** 時，傳回第二個參數；否則會傳回第三個參數。

### <a name="remarks"></a>備註

當條件為**true**時，只會評估真正的值。 當條件為**false**時，只會評估 false 值。 使用**if**函式時，您可以包含只有條件式有效的運算式。 例如，您可以參考存在於某個條件下但不在其他條件下的資源。 下一節會顯示有條件地評估運算式的範例。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)會示範如何使用 `if` 函式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

前述範例的輸出為：

| 名稱 | 在系統提示您進行確認時，輸入 | 值 |
| ---- | ---- | ----- |
| yesOutput | 字串 | 是 |
| noOutput | 字串 | no |
| objectOutput | 物件 | { "test": "value1" } |

下列[範例範本](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json)顯示如何使用此函式搭配僅有條件地有效的運算式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>否

`not(arg1)`

將布林值轉換為其相反值。

### <a name="parameters"></a>parameters

| 參數 | 必要 | 在系統提示您進行確認時，輸入 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |yes |布林值 |要轉換的值。 |

### <a name="return-value"></a>傳回值

當參數是 **False** 時，傳回 **True**。 當參數是 **True** 時，傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)會示範如何使用邏輯函式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前述範例的輸出為：

| 名稱 | 在系統提示您進行確認時，輸入 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)使用 **not** 搭配 [equals](resource-group-template-functions-comparison.md#equals)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

前述範例的輸出為：

| 名稱 | 在系統提示您進行確認時，輸入 | 值 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | true |

## <a name="or"></a>或

`or(arg1, arg2, ...)`

檢查任何參數值是否為 true。

### <a name="parameters"></a>parameters

| 參數 | 必要 | 在系統提示您進行確認時，輸入 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |yes |布林值 |要檢查是否為 ture 的第一個值。 |
| arg2 |yes |布林值 |要檢查是否為 ture 的第二個值。 |
| 其他引數 |否 |布林值 |要檢查是否為 true 的其他引數。 |

### <a name="return-value"></a>傳回值

如果任何值為 true，傳回 **True**，否則會傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)會示範如何使用邏輯函式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前述範例的輸出為：

| 名稱 | 在系統提示您進行確認時，輸入 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | true |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>後續步驟

* 如需有關 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要合併多個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 若要依指定的次數重複建立資源類型，請參閱 [在 Azure 資源管理員中建立資源的多個執行個體](resource-group-create-multiple.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](resource-group-template-deploy.md)。

