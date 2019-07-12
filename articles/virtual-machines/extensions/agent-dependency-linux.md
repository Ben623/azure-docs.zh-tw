---
title: 適用於 Linux 的 azure 監視相依性的虛擬機器擴充功能 |Microsoft Docs
description: 使用虛擬機器擴充功能，以部署 Linux 虛擬機器上的 Azure 監視相依性代理程式。
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 5faeebe799bd8cc0ba9a148508ac5b3a6d4b803a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120204"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>適用於 Linux 的 azure 監視相依性的虛擬機器擴充功能

適用於 VM 的 Azure 監視器對應功能會從 Microsoft Dependency Agent 取得其資料。 適用於 Linux 的 Azure VM 的相依性代理程式虛擬機器擴充功能是發行，並由 Microsoft 支援。 Azure 虛擬機器上安裝相依性代理程式擴充功能。 本文件詳述支援的平台、 組態和適用於 Linux 的 Azure VM 的相依性代理程式虛擬機器擴充功能的部署選項。

## <a name="prerequisites"></a>先決條件

### <a name="operating-system"></a>作業系統

適用於 Linux 的 Azure VM 的相依性代理程式擴充功能可以執行支援的作業系統中所列[Supported operating systems](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) Azure 監視的 Vm 部署文件的區段。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Azure Linux VM 上的 Azure VM 的相依性代理程式擴充功能的結構描述。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>屬性值

| 名稱 | 值/範例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>範本部署

您可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 若要在 Azure Resource Manager 範本部署期間執行 Azure VM 的相依性代理程式擴充功能，您可以使用 Azure Resource Manager 範本中的上一節中詳述的 JSON 結構描述。

虛擬機器擴充功能 JSON 可以是巢狀置於虛擬機器資源內部。 或者，您可以將它放在根目錄或最上層的 Resource Manager JSON 範本。 JSON 的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources)。

下列範例假設虛擬機器資源內部巢狀相依性代理程式擴充功能。 當您使用巢狀延伸模組資源時，JSON 會放在`"resources": []`的虛擬機器的物件。


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

當您將擴充 JSON 置於範本的根目錄時，資源名稱包含父系虛擬機器的參考。 類型可反映巢狀的組態。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

您可以使用 Azure CLI 來部署到現有的虛擬機器的相依性代理程式 VM 擴充功能。  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

從 Azure 入口網站，並使用 Azure CLI，則可以擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令：

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>支援

如果您需要在這篇文章中的任何時間點的更多協助，請連絡 Azure 專家上[MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]  。 如需如何使用 Azure 支援的詳細資訊，請閱讀[Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
